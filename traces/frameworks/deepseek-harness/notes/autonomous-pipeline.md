# Building a requirement → ship pipeline on dsh

*What the flow actually looks like if you compose `plan-mode` + `goal` + `ralph` + `workflow` into "state a requirement, clarify it interactively, then grind until the feature is done." Traced at `47f9438` through the codegraph index; every mechanism below was read in source or in its owning Agent Note.*

---

## 0. The short answer

The parts exist and they are unusually well-behaved, but **dsh gives you three different continuation loops and no glue between them**. The pipeline is something you compose out of documented extension points; it is not a mode you turn on. Two things are genuinely absent: an independent verification gate and anything resembling deployment.

The spine that makes the stages compose is this joint:

> Plan mode's approved markdown plan gets written into the **workspace**, and workspace-as-authority is exactly what every fresh worker treats as long-term memory. The clarification phase's output *is* the immutable objective plus a plan file in the working tree that survives every context reset.

> **口訣**:澄清階段的產物 = 一句不可變的 objective + 一份留在 working tree 的計畫;之後每一輪 worker 都失憶,只有工作區不失憶。

---

## 1. Stage 1 — the requirement enters

Two entry surfaces, and the choice matters for everything downstream:

| Surface | Mechanism | Interactive? |
|---|---|---|
| Web UI | full `dsh-web-app` bundle | yes — questions, approvals, plan review all have adapters |
| `dsh --profile headless "task"` | one-shot runner, no listening port | **no** — see §6.4 |
| ACP / JSON-RPC SDK | `packages/acp`, `packages/sdk` | only if the client implements the question adapter |

The headless runner creates one fresh persisted agent, submits the task as an ordinary `user/message`, waits for quiescence, writes the last non-empty assistant text to stdout, and exits `0` on a completed final `turn/end`.

To make the objective outlive the prompt, create a **goal** (`ctx.goals`): a branded id, the objective text, a durable phase (`active` / `paused` / `blocked` / `complete`), a compare-and-set revision, and `maxGoalRounds` (default 256). Every mutation appends a versioned `goal/change` session event carrying a full snapshot — the session log is the only durable store, so fork and resume inherit goal history with no second database.

---

## 2. Stage 2 — 反覆確認細節, as a real loop with a real exit gate

This is the part I expected to be hand-wavy and isn't. `plan-mode` (`ctx.planMode`) turns "keep clarifying until we agree" into a state machine:

- **The state is logged, not held in memory.** `plan/mode` is a log-only, whole-value-replace session event; the state in force is a *pure fold of the session log* (`foldPlanMode(events)`), so resume, fork, and compaction all recover it with no live mirror.
- **Entering** flips the fold and adds a deployment-owned `plan:policy` prompt section at order 50. The tool catalog does not change — `exit_plan_mode` stays registered whether plan mode is on or off, so the request prefix stays stable.
- **The exit gate**: `exit_plan_mode` requires a complete markdown plan starting with a `#` heading, and presents it to the human through the `ctx.userQuestions` seam.
- **The loop**: approval returns `{ approved: true }` and records a silent pending exit, appended at the next accepted in-turn pre-step. **Keep-planning is a *failed tool call* carrying the user's feedback** — so the model revises the plan and presents again. That's your 反覆確認細節 loop, and it terminates only on explicit human approval.
- **Why the append is deferred**: every session event is turn-enclosed, so a pending selection waits for the next accepted `agent/pre-step` before it can affect request derivation. A prepended pre-step listener appends it only *after* downstream listeners accept the step, and an append failure can never block the turn.

For a question the model raises on its own (not a plan review), `tool-ask-user` exposes `ctx.userQuestions` directly. For "may I run this," `ctx.approval` sits inside the tool pipeline between `tools/pre-execute` and the monotonic guards — and an absent or unanswerable approval channel **denies**, it does not fall through.

> ⚠️ **Plan mode is soft guidance.** The subsystem doc says so outright: sandbox mode and approval policy enforce restrictions independently, and *neither reads nor writes plan state*. Being "in plan mode" does not stop the agent from writing files. If you want plan mode to actually mean read-only, you configure `ctx.sandbox` and the permission preset separately.

---

## 3. Stage 3 — the execution loop, three ways

Here is the fork in the road. dsh ships three continuation mechanisms that look similar and behave completely differently.

| | `goal` + `goal-round-driver` | `ralph` | `workflow` |
|---|---|---|---|
| **Context per round** | same session, conversation accumulates | **zero** — a brand-new child session, no seed | whatever the script passes |
| **Long-term memory** | session log + workspace | **workspace working tree only** | script's own state |
| **Who owns the loop** | a driver plugin (deployment-owned) | a fixed script string (deployment-owned) | **the model** — it writes the script |
| **What the model controls** | when to mark complete/blocked | `objective` and `maxRounds`, nothing else | everything |
| **Stop condition** | phase ≠ active, or `roundsStarted ≥ maxGoalRounds` | report `complete` / `blocked`, or round cap | script returns |
| **Trigger** | fires at quiescence while `armed` | one foreground call, blocks until the run ends | one foreground call |
| **Default cap** | `defaultMaxGoalRounds` 256 | `maxRounds` 256 | engine's child ceiling |
| **Survives restart?** | phase yes, **authority no** (§6.3) | no — foreground only | no |

### 3.1 `goal-round-driver` — same-session grind

`drive()` (`packages/goal/goal-round-driver/src/index.ts:138`) is careful in a way worth copying:

1. **Only acts at true quiescence** — `readyToDrive()` requires the fiber `ACTIVE`, the agent still registered under its id, `agent.status === 'idle'`, no competing queued prompt, and not stopping.
2. **Durability checkpoint first** — `await ctx.sessions.flush(agent.session)` before reserving a round. If the flush fails it *disarms* rather than pressing on. After the flush it re-checks everything, because a human prompt may have landed while the checkpoint settled — and that prompt gets its own turn first.
3. **Reserves at most one round**, as an ordinary `user/message` with `source: { kind: 'goal', goalId, revision, round }`, submitted through `agent.followup()` — i.e. it goes into the same event-sourced `Inbox` as a human message. Goal rounds are not a privileged path.
4. **The round cap blocks, it doesn't silently stop**: `ctx.goals.block(agent, ref, { code: 'round-limit', message: … })`.

The prompt each round is short and explicitly distrusts the transcript:

> `Objective: …` / `Round: n/max` / *"Treat the current workspace, tool results, and durable session state as authoritative; inspect them instead of assuming earlier narration is still current. … Before claiming completion, gather evidence that the whole objective is achieved, read the current goal, and mark it complete."*

### 3.2 `ralph` — fresh-agent grind

`ralph({ objective, maxRounds? })` is the one that matches "一層一層跑" most literally. The script is a `String.raw` constant in `packages/workflow/tool-ralph/src/index.ts:90`, and the comment above it states the design:

> *"Fixed, deployment-owned orchestration. The model supplies data only; it cannot alter the loop, provider route, schema, or handoff validation."*

Each round:

```
agent(prompt, { schema: reportSchema })   ← one fresh child, structured output forced
```

with a prompt carrying exactly five things: "you are one fresh worker, you receive no parent conversation and no prior child session, **do not call the ralph tool: this round already is its worker**"; the immutable objective; `round n of max`; workspace-as-authority; and the previous structured handoff — qualified as *"Treat the previous report only as a bounded handoff; confirm it against the workspace."*

The handoff schema is where the real engineering is. `RalphRoundReport` = `{ status, summary, evidence[], nextSteps[], blocker }`, and `validateReport()` enforces **cross-field semantics**, not just types:

| status | requires |
|---|---|
| `continue` | ≥1 `nextSteps`, `blocker` must be `''` |
| `complete` | ≥1 `evidence`, `nextSteps` **empty**, `blocker` `''` |
| `blocked` | a concrete non-empty `blocker` |

Plus: every string must be trimmed and non-empty, and the serialized report must fit `maxHandoffChars` (16384) — **oversized fails the run rather than being truncated**, because a silently truncated handoff is a corrupted memory.

That table is the interesting bit. Claiming `complete` costs you: you must produce evidence *and* declare zero remaining next steps, in one atomic structured output. Claiming `blocked` costs you a concrete blocker string. The cheapest thing to say is `continue`, which is exactly the bias you want in an autonomous loop.

Terminal outcomes: `complete` / `blocked` return immediately; running out of rounds after a `continue` returns `budget-limited`; a child that settles unsuccessfully surfaces as `null` in the workflow language, which the script maps to `round-failed` **with the last good handoff retained**. Ralph adds no retry.

Two guardrails carried on `WorkflowStartRequest` rather than trusted to the script: `subagentProvider` (must exist, support structured output, and report `inheritsParentContext: false` — a fork-like provider **fails loudly before the run starts**) and `maxTotalAgents` (set to the resolved `maxRounds`, so the fixed loop's budget and the engine's runaway-child backstop cannot disagree).

### 3.3 `workflow` — model-authored orchestration

The general case: the model writes an orchestration script that runs in a worker thread, with `parallel()` and `pipeline()` hooks (`pipeline` = per-item stage chains with **no cross-stage barrier**; an ordinary stage throw drops that item to `null` and skips its remaining stages). Use it when the shape of the work is discovered at runtime; use `ralph` when you want the policy fixed and reviewable.

---

## 4. Stage 4 — verification

**dsh ships no verification gate, and it is honest about it.** From the Ralph note:

> *"Successful completion and blocker envelopes say that a worker reported the outcome rather than presenting it as independent certification."*

The `complete` status is a claim by the last worker, validated for *shape* (evidence present, next steps empty) and not for *truth*. If your pipeline is going to ship code, the verification gate is something you add. The extension points are all there and none of them are pre-wired:

- a `tools/post-execute` listener that runs the test suite and rewrites a `complete` into a `continue`
- a `ctx.jobs` background CI run whose result is injected with `agent.inject()`
- a `guard` plugin (the `guard/` group already does exactly this shape for loop hygiene and tool deadlines)
- an `agent/turn-stopping` serial listener — the documented place to stop a turn
- for the goal loop specifically: a policy consumer that calls `ctx.goals.block(agent, ref, { code: 'tests-failed', … })`, which is precisely what the domain's free-form blocker codes exist for

---

## 5. Stage 5 — 上線

Nothing. There is no deploy, release, or CD capability in the 219 packages. What you have is `ctx.shell` (bash seam + local/pwsh providers), `ctx.terminals` (persistent PTY), `ctx.jobs` (background work with `job_*` control tools), and `ctx.sandbox` (bwrap / Landlock / Seatbelt). Deployment is "a tool plugin you write, calling a shell command, behind an approval." That is the correct layer for it to live at, but it is not in the box.

---

## 6. The assembled flow — and where each stage breaks

```
[interactive surface: web UI or an ACP client with a question adapter]
  human states the requirement
        │
        ├─ ctx.goals.create(objective)          → goal/change event, phase=active, armed
        │
  /plan  → plan/mode event (a fold of the log, not memory)
        │   plan:policy prompt section active
        │   ┌───────────────────────────────────────────┐
        │   │ model drafts plan → exit_plan_mode        │
        │   │   → ctx.userQuestions review              │
        │   │   ← keep-planning = FAILED tool call      │──┐ loop until
        │   └───────────────────────────────────────────┘  │ approved
        │        approved → pending exit, appended at the next accepted pre-step
        │
        ├─ write the approved plan into the WORKSPACE  ← the joint (§0)
        │
        ▼
[execution — pick ONE loop, or nest them]
  goal-round-driver                    ralph
   at quiescence:                       foreground, blocks:
     flush session (durability)           round 1..N, each a FRESH child
     re-check readiness                   prompt = objective + round + workspace-as-authority
     followup() one goal round              + previous structured handoff (distrusted)
     phase≠active or cap → block          report: continue | complete | blocked
                                          cap exhausted → budget-limited
        │
        ▼
[verification]  ← YOU BUILD THIS. tools/post-execute | guard | ctx.jobs | turn-stopping
        │
        ▼
[上線]         ← YOU BUILD THIS. a tool over ctx.shell, behind ctx.approval
```

### The five things that will bite you

**6.1 Plan mode does not restrain anything.** Soft guidance only; sandbox and approval are configured separately and never read plan state.

**6.2 `complete` is self-reported.** See §4. Without a gate you have built a machine that declares victory.

**6.3 Automatic authority is deliberately not persisted.** `GoalView.activation` (`armed` / `disarmed`) is process-local and never part of the durable snapshot. *"A cache built from any seed starts disarmed, and every `agent/session-start` edge disarms it again."* The rejected alternative is stated explicitly:

> *"Persist activation and restart automatically — rejected because opening or resuming a session must wait for human input; durable phase records status, not fresh authority to spend resources."*

So a restarted process resumes the **objective** but never the **work**. For a 24/7 unattended pipeline this is a feature you must design around: something outside dsh has to decide to re-arm, because dsh will never decide it for you.

**6.4 The pipeline spans two process modes.** Headless has *"one submitted task only — no interactive follow-up surface."* The interaction group's own README says interactive applications provide the concrete question/approval adapters, and automation uses ACP. So the clarification phase needs an interactive surface (web UI, or an ACP client that implements the question adapter) while the grind phase can run headless. It is not one process end to end.

**6.5 `0.1.0-rc.5`, developer preview.** README, in capitals: *THERE WILL BE COMPATIBILITY-BREAKING CHANGES.* `SESSION_FORMAT_VERSION` is `0` with no compatibility promise, and *"backends reject old on-disk formats."* Your durable session logs — the thing this whole design leans on — may not survive an upgrade.

---

## 7. What you would actually have to write

Assuming you build this on dsh today, roughly in order of effort:

1. **A verification gate plugin** — the biggest gap and the one that decides whether the thing is trustworthy (§4).
2. **A deploy tool** over `ctx.shell`, behind `ctx.approval` (§5).
3. **A re-arm policy** — whatever outside process decides a restarted session may resume spending (§6.3).
4. **A composition preset** (`packages/preset`, per-session agent composition from preset `cordis.yml`) plus a profile bundle stacking your plugins over `dsh-base` — this is how the pipeline becomes a *thing you run* rather than a sequence you drive by hand.
5. **A plan-file convention** — the joint in §0 only works if the approved plan is written somewhere every fresh worker will look.
6. **A permission preset + sandbox config** matching how unattended you want it (§6.1).

None of that is large. The load-bearing work — durable replayable state, structured handoffs that fail loud, cancellation to quiescence, provider capability checks before a run starts — is already done, and done more carefully than most people would do it themselves.

> **口訣**:dsh 把「跑得久還不失憶」這件事做完了;「怎麼判斷真的做完了」和「怎麼上線」還是你的。

---

## References

- [`packages/workflow/tool-ralph/src/index.ts`](../../../../frameworks/deepseek-harness/packages/workflow/tool-ralph/src/index.ts) — `RALPH_SCRIPT`, the report schema, `validateReport`
- [`packages/goal/goal-round-driver/src/index.ts`](../../../../frameworks/deepseek-harness/packages/goal/goal-round-driver/src/index.ts) — `drive()`, `readyToDrive()`, `renderGoalRoundPrompt()`
- [`docs/subsystems/plan.md`](../../../../frameworks/deepseek-harness/docs/subsystems/plan.md) — the fold, the pre-step append, the exit tool
- [`docs/subsystems/goal.md`](../../../../frameworks/deepseek-harness/docs/subsystems/goal.md) — `GoalRef`, `GoalPhase`, `GoalBlockReason`, `GoalView`
- [`.agents/notes/implemented/feature/2026-07-19-fresh-agent-ralph-workflow-tool.md`](../../../../frameworks/deepseek-harness/.agents/notes/implemented/feature/2026-07-19-fresh-agent-ralph-workflow-tool.md)
- [`.agents/notes/implemented/feature/2026-07-19-persisted-same-session-goal-domain.md`](../../../../frameworks/deepseek-harness/.agents/notes/implemented/feature/2026-07-19-persisted-same-session-goal-domain.md)
- [`packages/interaction/README.md`](../../../../frameworks/deepseek-harness/packages/interaction/README.md) / [`packages/bundle/headless/README.md`](../../../../frameworks/deepseek-harness/packages/bundle/headless/README.md) — the surface split
