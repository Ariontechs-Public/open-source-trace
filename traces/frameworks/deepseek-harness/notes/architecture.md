# DeepSeek Harness — Architecture Deep Dive

*Traced at `47f9438` (2026-08-13, `0.1.0-rc.5`). Source read through the live codegraph index at `frameworks/deepseek-harness/.codegraph/` (3,839 files, 40,008 nodes, 109,829 edges).*

---

## 0. Shape of the thing

| | |
|---|---|
| Packages | 219 (`packages/<group>/<pkg>`, npm scope `@deepseek-ai/dsh-*`) |
| Non-test TypeScript | ~301,565 LOC |
| Test TypeScript | ~262,557 LOC |
| Markdown files | 2,355 (roughly half are `.zh.md` counterparts) |
| Repo scripts / gates | 142 files under `scripts/` |
| Commits | 12,293 — 581 in 2026-06, 8,273 in 2026-07, 3,439 in 2026-08 |
| Authors | 37; top contributor holds 5,235 commits |
| Runtime | Node `^22.19 || >=24`, ESM everywhere, pnpm 11 workspaces |

Two months old, 12k commits, and already carrying generated catalogs, a frozen design-note archive, and a per-file 100% coverage gate. The pace and the discipline are both unusual; the interesting question is how they coexist, and the answer is that most of the discipline is *mechanical* — see §5.

Top-level layout:

```
vendor/      pinned copies of Cordis + friends, rescoped to @deepseek-ai/cordis-*
packages/    the 219 workspaces, grouped by capability
apps/        cli (the `dsh` binary) and web
native/      landlock-run node addon (Linux confinement)
python/      Python SDK + bundled runtime
examples/    runnable cordis.yml leaves over demo bundles
.agents/     agent workflows (skills/) and 684 Agent Notes (notes/, each + a .zh.md)
docs/        hand-written architecture + generated catalogs + postmortems
scripts/     142 gates and generators
website/     VitePress projection of selected bilingual docs
```

---

## 1. Cordis: the framework under the framework

dsh doesn't own its plugin framework. [Cordis](https://github.com/cordiverse/cordis) is vendored into `vendor/` as pinned source (9 packages: `cordis`, `cosmokit`, `group`, `hmr`, `include`, `loader`, `logger-console`, `schemastery`, `timer`), rescoped to `@deepseek-ai/cordis-*`, marked `private: true`, and synced through a documented procedure with upstream SHAs in `vendor/README.md`.

`docs/cordis-primer.md` compresses it to five ideas:

1. **A plugin is an object implementing `Service`** — either a function with optional `inject` and `apply(ctx)`, or a `Service` subclass whose lifecycle Cordis mounts into the current context.
2. **A context is a repository of services.** A service claims a stable key (`ctx.tools`, `ctx.llm`, `ctx.sessions`); consumers find it by key rather than importing a concrete implementation.
3. **`inject` declares dependencies**, so load order is *derived* from service requirements instead of hand-sequenced boot code. A plugin whose injections aren't satisfied stays pending.
4. **Typed events** declared through TypeScript declaration merging, dispatched as `emit` / `waterfall` / `parallel` / `serial`. The dispatch mode is part of the event's public contract — new events carry an `@mode` JSDoc tag, and a generated catalog checks declarations against dispatch sites.
5. **Registrations are reversible effects.** Prompt sections, tool schemas, adapters, providers, listeners — all installed through `ctx.effect()` or `ctx.on()`, all returning disposers, so reload and teardown unwind predictably.

The dispatch table is worth memorizing because it's the whole extension vocabulary:

| Mode | Awaited? | Order | Returns? |
|---|---|---|---|
| `emit` | No | registration order | No |
| `waterfall` | No | registration order | Yes |
| `parallel` | Yes | all listeners in parallel | No |
| `serial` | Yes | registration order | Yes |

`ctx.waterfall` is **around-middleware**: a listener receives `(...args, next)`, calls `next()` to delegate (possibly wrapping the result), or returns without `next()` to short-circuit. AGENTS.md makes it a hard rule — *"Waterfall listeners MUST call `next()`"* — because a policy listener that owns a decision short-circuits deliberately, while an observer that forgets `next()` silently breaks the chain.

> **口訣**: 服務靠 key 找、順序靠 inject 排、註冊都可撤銷、攔截都是 waterfall。

---

## 2. Boot: profiles, bundles, and a patch stack

A running `dsh` is a plugin tree composed at boot from **ordered layers**:

- A **bundle** is a distribution format for Cordis config rows plus the code they mount. It declares itself via `dsh.bundle` in its `package.json`, pointing at a patch file.
- A **profile** is a named composition stored in the Harness home. It lists bundles (`dsh.profile`), holds out-of-tree plugins it installs, and keeps the user's `cordis.patch.yml`. `web` and `headless` ship as templates.

Layers apply to an empty entry list in this order:

```
each bundle in the profile's listed order
  → the profile's cordis.patch.yml
    → the home-level cordis.patch.yml
      → any --patch overlay
```

A patch targets a row **by id** and either replaces its whole config or inserts new rows. `dsh-base` is the first layer of every profile (model adapters, tools, persistence, sandbox and approval policy, settings, credentials, telemetry); `dsh-web-app` adds the browser application; `dsh-headless` adds a one-shot runner with no server at all.

The escape hatch that makes this legible:

```sh
dsh --profile web --dump-config    # prints the tree your machine actually boots
```

Every row it prints can be replaced by a patch of your own. That's the concrete meaning of "no privileged core."

---

## 3. The turn loop

`packages/core/agent-loop` is the *default* driver implementing the `Agent` interface — one plugin among many, registered on `ctx.agentLoop`, swappable. Vocabulary first:

- A **step** = one model request plus the tools it calls.
- A **turn** = zero or more steps; it opens before its first input is claimed and closes once nothing is owed.

### 3.1 The documented flow

```text
turn/start
  claim next-step input plus one queued message
  assemble prompt sections + tool schemas
  → agent/pre-step                    reject | enter(messages)
     step/start
     append entered messages as user/message
     derive model history from the log
     agent/request → llm/stream → assistant/chunk* → assistant/message
     tool/call* → tools/pre-execute → tools/execute → tools/post-execute → tool/result*
     step/end
     tools owe another request, or next-step input arrived → claim → next step
  → agent/turn-stopping
turn/end
```

`turn/*`, `step/*`, `user/message`, `assistant/*`, and `tool/*` are **durable session events**. `agent/pre-step`, `agent/request`, `llm/stream`, and the three `tools/*` events are **waterfalls**; `agent/turn-stopping` is **serial** with no `next()`.

### 3.2 What the source actually does

`ReactLoopAgent.turn()` ([packages/core/agent-loop/src/agent.ts:246](../../../../frameworks/deepseek-harness/packages/core/agent-loop/src/agent.ts)) — the details the diagram elides:

- **A rejected or empty first claim still closes a durable turn.** If `agent/pre-step` rejects, or the first `enter` decision is rewritten to zero messages, the loop appends `turn/start` … `turn/end` having spent no model call. *The log records the attempt.* That's the invariant refusing to let a no-op vanish.
- **`max-tokens` is sticky.** Once any step hits the ceiling, a later step that completes normally must not downgrade the turn's outcome — enforced by an explicit guard on `turnEnds`.
- **Every failure is structured.** An `LlmError` keeps its facts; anything else flattens to `errorChain` text under code `UNKNOWN`. `turn/end` is appended in a `finally`, always, with a reason.
- **Cancellation uses a fresh `AbortController` per turn.** After a turn that leaves pending input, `phase.abort` is replaced and `wakeRequested` cleared, so a wake latch set on the old controller can't stale-fire into the next turn.

`ReactLoopAgent.step()` (same file, line 332):

- Streams from `preparedCall?.stream(request) ?? ctx.llm.stream(request)`, appending **every chunk** as a durable `assistant/chunk` event and collecting their seqs.
- The assembled `assistant/message` is appended with `sourceEventSeqs: chunkSeqs` — the message links back to the raw chunks it was built from, so replay and UI fidelity both survive.
- Request errors go through an `agent/request-error` waterfall that can return `{kind: 'retry'}`; without it, the failure throws as `LlmError`. Retry policy is data on the prepared call, not a hardcoded loop.
- No tool calls in the message → `{kind: 'completed'}`. Otherwise `executeToolCalls(...)` runs, and its `concluded` flag decides whether the turn owes another request.

### 3.3 The Inbox: even the queue is event-sourced

This is the detail that convinced me the invariant is real. `Inbox` ([packages/core/agent/src/inbox.ts](../../../../frameworks/deepseek-harness/packages/core/agent/src/inbox.ts)) is a *replay-once projection* over durable `agent/inbox/spliced` events, rebuilt from the session log on construction. Two lanes:

| Lane | Holds | Claimed at |
|---|---|---|
| `next-turn` | queued prompts, each awaiting its own turn | turn boundary (one at a time) |
| `next-step` | steering + injected context | every step boundary |

`claim(target, turn)` takes the whole `next-step` batch, plus exactly one queued turn when the target says so. Mutations go through one private `mutate()` that normalizes JS splice semantics (negative starts, NaN, over-long deletes), validates that no message id is pending twice, **appends the durable event before mutating the live projection** — so synchronous `session/event` observers still see the pre-splice lists and can reconstruct what was removed — then publishes `inserted`/`discarded`/`claimed` notifications.

Steering an agent is therefore not "poke the queue"; it's a logged, normalized, replayable splice. Resume rebuilds the same pending state.

### 3.4 The tool pipeline

`executeToolCalls` ([packages/core/agent-loop/src/tool-calls.ts:59](../../../../frameworks/deepseek-harness/packages/core/agent-loop/src/tool-calls.ts)) walks the tool-call blocks, asking `ctx.tools.executionMode(exec)` per call and grouping: `parallel` mode swallows the remaining calls into one group, anything else runs alone. A comment explains the ordering choice — *"Commit before classifying again so registry changes affect unstarted calls"* — i.e. a tool that mounts new tools can change how the *rest of this batch* runs. If a group aborts, every remaining call is appended as a skipped `tool/call`, so the log stays complete.

Around each call, `docs/tool-execution-pipeline.md` (generated Mermaid) shows where policy lives:

```
tool/call logged  →  tools/pre-execute waterfall   (hooks, permission, sandbox)
                  →  monotonic guards              (deny or abstain; identity protected)
                  →  ctx.approval one-shot prompt  (absent/unanswerable ⇒ deny)
                  →  tools/execute waterfall       (timeout, retry, metrics — around dispatch)
                  →  the tool body                 (fs/write-intent, fs/edit-intent gates below it)
                  →  tools/post-execute waterfall  (accept, block, replace, add context)
                  →  registry normalization        (snapshot throws become isError)
                  →  finalizeContent               (content-only invariant)
                  →  tools/result                  (frozen authoritative outcome)
                  →  tool/result logged
```

Denials still flow through `post-execute` — a rejected call produces a real, logged result rather than a hole. Additional contexts collected by tools are injected as a `user/message` *after* the recorded tool results, preserving call/result adjacency.

---

## 4. Capability seams

The load-bearing abstraction, and dsh is dogmatic about it. A **seam** has three roles:

| Role | Responsibility |
|---|---|
| **Service Definition** | declares the interface |
| **Service Provider** | implements it |
| **Consumer** | uses it — commonly a model-facing tool |

*"A package may combine roles, but one role alone is not a seam; adding a capability means designing all three."* Hence the package explosion: `shell/` is executor seam + local impl + pwsh impl + model-facing tool; `fs/` is seam + local impl + file tools + bash-backed discovery tools; `subagent/` is 11 packages.

The payoff is stated as a single sentence in `docs/architecture.md` and it's the best argument for the cost: **filesystem and subprocess providers share one execution world, so pointing them at a remote sandbox moves Bash, PTY, and LSP with them, with no provider forks.** Swap two providers, and every consumer above them relocates.

Same story for subagents: `subagent-fork-in-process`, `subagent-spawn-in-process`, `subagent-dsh-sdk`, `subagent-acp`, `subagent-claude-code`, `subagent-codex` all sit behind one interface, spanning "a fresh child agent in this process" to "a delegated turn inside Codex."

### Where new behavior goes

`docs/architecture.md` ends with a goal → mechanism table, which is really the harness's public API surface. A sample:

| Goal | Mechanism |
|---|---|
| Add a model provider | register its adapter on `ctx.llm` |
| Add a model-facing capability | register on `ctx.tools`; its schema joins prompt assembly |
| Add shell execution | register a `ctx.shell` backend (local one spawns through `ctx.subprocess`) |
| Add a human command | register on `ctx.commands`; dispatches with no model turn |
| Add background work | register on `ctx.jobs`; `job_*` tools collect or stop it |
| Confine spawned processes | use a `ctx.sandbox` backend; consumers wrap argv before spawning |
| Intercept a request, tool, or turn | its `agent/*` or `tools/*` event |
| Add model-facing context | `agent.inject()` — lands in the next admitted request |
| Add durable session state | extend `SessionEventMap`; render and replay from the log |
| Scope a registration to one agent | use that agent's `agent.ctx` |

*"Changing the loop itself updates this map"* — the table is the contract.

---

## 5. The discipline layer

### 5.1 Agent Notes — RFCs written by agents, gated by scripts

`.agents/notes/` holds **684** design notes — 506 implemented, 142 archived, 25 proposed, 11 rejected — each with a `.zh.md` counterpart beside it. Path encodes two axes — `{lifecycle}/{class}/yyyy-mm-dd-topic-title.md`:

- **Lifecycle**: `proposed/` → `implemented/` (or `rejected/`), with `archived/` as a separate frozen tree.
- **Class**: a closed set — `feature`, `bug-fix`, `simplification`, `architecture`, `process`, `testing`. (`refactor` is deliberately absent: it overlaps `simplification`, whose discriminator "does observable behavior change?" already covers it.) The classification gate rejects any other folder.

The rules that make it more than a docs folder:

- **Every non-trivial change MUST add or update a note in the same PR.** Only purely mechanical edits are exempt.
- **`## Alternatives considered` is mandatory** — *"A decision recorded without what it beat invites re-litigation — the failure Agent Notes exist to prevent."* Pre-format notes that genuinely can't reconstruct theirs carry one exact machine-recognized comment instead.
- **The body skeleton differs per lifecycle and the gate enforces it.** `implemented/` notes must speak present tense: `## Proposal`, `## Plan`, `## Migration plan`, and `## Acceptance criteria` are *rejected* in an implemented note. Moving `proposed/` → `implemented/` means rewriting `## Proposal` into `## Decision` and folding acceptance criteria and risks into `## Consequences`, in the same change.
- **Implemented notes are kept current with shipped reality** — when code moves a file or renames a key, the note is updated to match (facts only, never the decision).
- **A note is never edited into a different decision** — supersede it and cross-link. Every new note triggers a **supersession check** across the active tree.
- **The archive is frozen.** Archival moves the complete English/Chinese/sidecar triplet, stamps `Archived: YYYY-MM-DD`, re-records the sidecar hash, and repairs inbound links — and those are the *only* permitted content changes, ever. `verify-archived-agent-notes.ts` enforces the closed class tree, complete triplets, sidecar hashes, and an append-only frozen-content manifest.
- **No `INDEX.md`, deliberately** — a note owns that rationale. The lifecycle/class tree *is* the inventory.
- **Cross-references are relative markdown links, never prose or numbers**, so they're mechanically checkable and survive folder moves.

This is the single most transferable idea in the repo, and it's also the one I'd most want to test at smaller scale: it presumes notes are cheap to write (agents write them) and expensive to lose (humans re-litigate).

### 5.2 Generated documentation

Four of the largest docs are build artifacts, regenerated in memory by `pnpm run doc-sync` and failing CI on any diff:

| Doc | Size | Generator |
|---|---|---|
| `config-catalog.md` | 129 KB | `gen-config-catalog.ts` |
| `module-graph.md` | 122 KB | `gen-module-graph.ts` |
| `tool-catalog.md` | 79 KB | `gen-tool-catalog.ts` |
| `persistence-catalog.md` | 33 KB | `gen-persistence-catalog.ts` |

Plus `gen-doc-graphs.ts` (65 KB of generator) producing the Mermaid flows in `tool-execution-pipeline.md`, `agent-lifecycle.md`, and `event-producer-consumer.md`. A JSDoc or public-signature edit cannot ship without regenerating the catalog the *model* reads — documentation drift is a build failure, not a review comment.

### 5.3 The gate wall

`scripts/` holds 142 files. Representative gates: `verify-export-jsdoc` (every function-like export documents `@param`/`@returns`), `verify-agent-note-format`, `verify-archived-agent-notes`, `verify-package-readme-model-experience` (every package README explains its Model Experience, or is on an explicit allowlist), `verify-package-readme-limitations`, `verify-doc-budgets` (word ceilings per doc, raised only with justification), `check-workspace-constraints`, `publint-all`, `knip` (dead exports), `jscpd` (cross-file clone detection), `oxlint-contract`, `project-reference-faces` (TypeScript program topology).

Testing: `pnpm run test:coverage` — **per-file 100% on `packages/*/*/src`** — is the CI gate, not `pnpm run test`. On top: keyless snapshot replay of assembled application transcripts (*"package tests, e2e-only assertions, and mock-only fixtures do not substitute for the assembled application transcript"*), plus real-API e2e that self-skips without `DEEPSEEK_API_KEY`.

### 5.4 Conventions worth stealing verbatim

From root `AGENTS.md`:

- **"Explicit > implicit at package boundaries"** — defaulting is an explicit `resolve(request): Spec` step in the owning implementation, never a hidden `?? default` inside `run()`.
- **"No hardcoded tunables in plugins"** — deployment-varying choices are validated `Config` fields changeable from `cordis.yml`; *a `DEFAULT_*` constant or test hook is not configurability*. Protocol constants, external specs, and security invariants stay fixed.
- **"Trust TypeScript at typed same-process boundaries"** — do not add runtime validation or hostile-input tests for values the static interface already guarantees. Validate at the seven real boundaries: parser/config, queued, model/tool JSON, durable/file, worker, process, wire.
- **"An empty `catch` names what it swallows"** and why nothing else can reach it; keep the `try` to one statement.
- **"Tests describe behavior, not correctness"** — change obsolete behavior together with its tests, and explain why in the PR.
- **Opaque cross-boundary ids are branded** (`Branded<B>`), never bare `string`.
- **Prose rules**: no metaphors; before writing `contract`, `boundary`, or `shape`, ask whether a more exact term exists (`response fields`, not `response shape`). Comments state complete contracts, not reasoning transcripts.
- **Pre-release stance, with an expiry date on itself**: *"Remove this section at the first tagged release. With no external consumers, prefer the correct foundation over compatibility shims: rename or repackage freely and update every reference together."*

---

## 6. Self-reference: the agent mounting its own plugins

`packages/extensions/tool-cordis` gives the model three tools over the live Cordis runtime it is itself running inside:

| Tool | Contract |
|---|---|
| `cordis_inspect` | Read-only Markdown report over the live runtime: `services`, `plugins`, `tools`, `temporary`, `api` (live service signatures + referenced types), `events` (with dispatch mode and signature). An exact `name` returns one target with its original JSDoc. |
| `cordis_mount` | Evaluates model-written `code` *now* as an async function body in a fresh `node:vm` realm, mounts the returned plugin under an internal `cordis-dynamic` group with a process-local id (`dyn-1`, `dyn-2`, …). Saves it nowhere. |
| `cordis_unmount` | Unmounts one temporary plugin by id, returning only after every owned tool, listener, service, timer, and effect reaches **quiescence**. Cannot touch Loader, configured, or installed plugins. |

The design note names the three correctness problems it exists to answer, and they generalize well beyond this feature:

1. **Model-written registration must fail where it happens** — a malformed tool schema fails at registration, not later during prompt assembly.
2. **Model-written code calls APIs whose source it has never seen** — hence `cordis_inspect`'s `api`/`events` sections, served from a *generated* catalog gated for freshness (`verify-cordis-api` in `doc-sync`) and intersected with the live runtime at call time, so the model isn't blind-probing method signatures across many wasted steps.
3. **Everything mounted must be fully disposable** — by the model on demand, and by ordinary plugin lifecycle when the host reloads, or a long session accretes orphaned listeners and tools.

Sandbox globals are deliberately tiny: a tagged write-through `console` (so a listener firing long after the mount call still lands where the user sees it), `harness.defineTool`/`registerTool`, encoding primitives a fresh vm realm lacks, and *callable traps* over withheld Node APIs (`require`, `setTimeout`, `fetch`) that throw a redirect naming the Cordis alternative. Only function-shaped globals are trapped — `process` and `Buffer` stay `undefined` so a `typeof` feature probe stays inert instead of detonating.

Mounts compose with each other through ordinary service semantics: mount A calls `ctx.provide('foo', …)`, mount B declares `inject: ['foo']` and activates the moment it exists; unmounting A sends B back to pending with its registrations unwound.

And the honesty is worth quoting: *"Neither restricts the authority of exposed services… This is an opt-in development tool with bash-equivalent trust, not a security boundary or product default."*

Crucially, this ships **no new session event type** — mount/unmount are visible through their logged `tool/call`/`tool/result` pairs, and a changed tool set is logged by the full changed request header emitted when schemas change between steps. The invariant holds even here.

---

## 7. Interop posture

dsh does not assume it's the only harness on the machine:

- `packages/hooks/hooks-claude-code` and `hooks-codex` — bridges over a shared hook wire-protocol library, so existing Claude Code / Codex hooks work.
- `packages/subagent/subagent-claude-code`, `subagent-codex`, `subagent-acp` — delegate a subagent turn *to another product*, behind the same interface as an in-process fork.
- `packages/acp/` — an automation-only Agent Client Protocol server.
- `packages/skill/` — a skill provider registry + local filesystem provider + a model-facing catalog/loader tool.
- `packages/workflow/` — `tool-workflow` and `tool-ralph` over a worker-thread engine whose script hooks are `parallel()` and `pipeline()`, with per-item stage chains and no cross-stage barrier — structurally the same design as Claude Code's Workflow tool, down to item caps and "an ordinary stage throw drops the item to `null`."

---

## 8. What I'd steal

1. **"Model-visible ⟺ logged," asserted at runtime.** Even if you never build a plugin framework, this one invariant eliminates a category of resume/replay bugs. Corollary: a new kind of model-visible input requires a new event type, on purpose, to make the cost visible.
2. **Event-source the input queue, not just the output.** Steering and injection as logged splices with normalized coordinates is strictly better than mutating a live array.
3. **The seam triad as a checklist.** "Is this a Definition, a Provider, or a Consumer — and where are the other two?" catches half-built abstractions at design time.
4. **Agent Notes with a machine-checked lifecycle.** Path-encoded status and class, mandatory alternatives, present-tense enforcement for implemented notes, a frozen archive, and no index file.
5. **Generated docs behind a freshness gate.** If the model reads it, generate it, and fail CI on drift.
6. **"No hardcoded tunables" with an explicit anti-pattern named.** `DEFAULT_*` is not configurability — that sentence alone would fix a lot of code.
7. **A pre-release stance section that specifies its own deletion.** Time-boxed policy, written down.

---

## References

- [`docs/architecture.md`](../../../../frameworks/deepseek-harness/docs/architecture.md) — the map, and the "where new behavior goes" table
- [`docs/cordis-primer.md`](../../../../frameworks/deepseek-harness/docs/cordis-primer.md) — five ideas + dispatch modes + waterfall semantics
- [`docs/capability-seams.md`](../../../../frameworks/deepseek-harness/docs/capability-seams.md) — 38 KB capability graph
- [`docs/tool-execution-pipeline.md`](../../../../frameworks/deepseek-harness/docs/tool-execution-pipeline.md) / [`docs/agent-lifecycle.md`](../../../../frameworks/deepseek-harness/docs/agent-lifecycle.md) — generated Mermaid flows
- [`AGENTS.md`](../../../../frameworks/deepseek-harness/AGENTS.md) — conventions, gates, pre-release stance
- [`.agents/notes/README.md`](../../../../frameworks/deepseek-harness/.agents/notes/README.md) — the Agent Note system in full
- [`.agents/notes/implemented/feature/2026-07-08-self-referential-cordis-toolset.md`](../../../../frameworks/deepseek-harness/.agents/notes/implemented/feature/2026-07-08-self-referential-cordis-toolset.md) — self-modification design
- [`packages/README.md`](../../../../frameworks/deepseek-harness/packages/README.md) — the 219-package group map with release expectations
