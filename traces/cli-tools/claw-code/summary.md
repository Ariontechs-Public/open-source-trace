# Claw Code

## TL;DR
Claw Code (`ultraworkers/claw-code`) is a **public demonstration of autonomous, multi-agent software development**, not just another CLI agent. The shipped artifact is a Rust workspace (`rust/`, the canonical `claw` CLI binary) plus a Python companion/reference workspace under `src/` — but the project's own [PHILOSOPHY.md](../../../cli-tools/claw-code/PHILOSOPHY.md) opens with *"stop staring at the files"*: the code is **evidence**, the **coordination system** is the lesson. That coordination is a three-part stack — **OmX** (`oh-my-codex`) for workflow, **clawhip** for event/notification routing, **OmO** (`oh-my-openagent`) for multi-agent role coordination. Humans direct from Discord; autonomous claws (Architect / Executor / Reviewer) plan, execute, review, retry, and push in parallel.

**中文口訣**: 人在 Discord 下指令，claws 自己分工、寫 code、跑測試、爭執、修好、推上去 — repo 只是「過程的證據」，不是要看的本體。

## Why I Care
I care about the same question this project is publicly testing: **when AI can rebuild a codebase in hours, what's actually scarce?** Claw Code's bet is *architectural clarity, task decomposition, taste, conviction* — and they've built the smallest plausible exemplar of a "humans-give-direction-machines-do-labor" pipeline I've seen in OSS. That's directly informative for anything I build that aspires to autonomous operation rather than copilot-style assistance.

## Killer Ideas
*First-pass observations from README + PHILOSOPHY.md + repo shape. No graph yet — depth will land after `/graphify cli-tools/claw-code` and a real read of `rust/` and `src/`.*

- **"The code is evidence; the coordination system is the lesson."** Framing the *meta-system* as the artifact (vs the code) is a rare and useful intellectual move — it tells readers what *not* to optimize their attention on.
- **Notification routing pushed OUT of agent context.** clawhip watches git/tmux/GitHub/agent-lifecycle and delivers status, so the coding agent's context window stays focused on implementation. Context economy as architecture.
- **Discord as the human interface** (not terminal). The human can type one sentence from a phone, walk away, and the claws coordinate the rest. This inverts the default "human sits in tmux babysitting" model.
- **Three-part separation of concerns**:
  - OmX (`oh-my-codex`) — turns short directives into structured execution (planning keywords, execution modes, persistent verification loops, parallel multi-agent workflows)
  - clawhip — event router
  - OmO (`oh-my-openagent`) — multi-agent coordination, handles Architect/Executor/Reviewer disagreement-and-converge
- **Architect / Executor / Reviewer role decomposition.** Not just "an agent" but a *team* with structured disagreement resolution.
- **Parity-harness as a first-class engineering artifact.** PARITY.md + `mock_parity_scenarios.json` + MOCK_PARITY_HARNESS.md track Rust-port equivalence against the Python reference. Reproducible scenario testing across implementations.
- **`claw doctor` as the canonical health check.** First thing to run after building; one-shot diagnostic surface rather than scattered "is X working" commands.
- **Rust canonical + Python reference.** Choice of "both kept in-repo, one labeled canonical" is unusual — most projects either pick one or maintain a deprecated shadow. The README is explicit about which is the runtime.

## Open Questions
*Lots of them — this is a baseline first-pass trace.*

- What exactly does **clawhip** route, and how does it decide what NOT to route into the agent's context? Read `clawhip` repo + look for the integration point in `rust/`.
- What's the **on-the-wire format** between Discord → OmX → claws? Webhook? Long-poll? Custom protocol?
- **Architect/Executor/Reviewer**: are these literal Claude/Codex/Gemini instances with different system prompts, or one model with different roles, or different models for different roles? Read OmO.
- **Parity-harness**: how does it compare runtime behavior between Rust and Python? Scenario-by-scenario diff, or behavioral assertion DSL?
- The repo has a massive `ROADMAP.md` (982KB) and `prd.json` (20KB) — are those agent-generated planning artifacts, or human-authored? If agent-generated, they're a window into how the coordination system *thinks*.
- `progress.txt` (19KB) at repo root — running log of what the claws have done? Worth reading as raw evidence of the system in operation.
- **What in `src/` (Python) was kept after the Rust rewrite, and why?** Audit helpers per README — but which specifically? That's a hint about what was structurally hard to port.
- **ACP/Zed integration** is explicitly "not yet" per the README warning callout — what's the planned shape, and what's blocking?
- The **`.omx/`** and **`.port_sessions/`** dot-dirs at repo root suggest persistent agent state lives in-repo. What's tracked vs gitignored?

## Map
- Knowledge graph: *not yet generated* — run `/graphify cli-tools/claw-code` (likely needs subdir scoping; `rust/` + `src/` together may exceed thresholds).
- Upstream: <https://github.com/ultraworkers/claw-code> · pinned at `f8e1bb7`
- Key docs in repo: [PHILOSOPHY.md](../../../cli-tools/claw-code/PHILOSOPHY.md) · [PARITY.md](../../../cli-tools/claw-code/PARITY.md) · [ROADMAP.md](../../../cli-tools/claw-code/ROADMAP.md) · `USAGE.md` · `rust/README.md`
- Related repos (not submodules here, may add later): [oh-my-codex](https://github.com/Yeachan-Heo/oh-my-codex), [clawhip](https://github.com/Yeachan-Heo/clawhip), [oh-my-openagent](https://github.com/code-yeongyu/oh-my-openagent)
- Compare with: [[superpowers]] (skill-based single-agent), [[gstack]] (persona-bundled subagents, single-host coordination), [[opencode]] (single-agent CLI)
- Deep dives (planned, not yet written):
  - `notes/three-part-system.md` — trace OmX → clawhip → OmO with the actual integration points in `rust/`
  - `notes/parity-harness.md` — how Rust ↔ Python parity testing actually works
  - `notes/agent-artifacts-as-evidence.md` — read ROADMAP.md / prd.json / progress.txt as logs of the coordination system in action
