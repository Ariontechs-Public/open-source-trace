# opencode

## TL;DR
Open-source agentic coding tool in the Claude Code space — and, as of 2026 Q2, **no longer just a CLI**. Single-binary install (`brew`, curl, scoop, choco, AUR, mise, nix...) plus first-class desktop apps for mac/Windows/Linux. Ships **two built-in agents** — `build` (full-access) and `plan` (read-only, denies edits, asks before bash) — Tab-switchable. MIT-licensed, defaults to the `dev` branch, *extremely* active (~5,800 commits Feb–Jun 2026). The headline arc this quarter: a **"v2" runtime rewrite on Effect**, a **unified HTTP API**, a serious **desktop app**, an **ACP** protocol layer, and its own **skill + command registry** — opencode is growing from a CLI into an agent *platform*. Sessions are now first-class objects you can snapshot / revert / fork / move.

**中文口訣**: 不只是 Claude Code 的 OSS 替代品 — 它正從工具長成平台:v2 runtime 當引擎、httpapi 當接口、desktop/acp 當前端、skill/command registry 當生態。

## Why I Care
Same problem space as Claude Code, different design constraints. The interesting reads are where opencode *deliberately diverges*: the explicit build/plan agent split (vs Claude Code's implicit "ask before destructive" pattern), the desktop-app form factor, and the provider abstraction. Provides a clean diff against Anthropic's design choices.

## Killer Ideas
- **Two-agent mode toggle (`build` vs `plan`)**, **Tab-switchable**: plan-mode denies edits and confirms bash by default. Surfaces the safety/exploration tradeoff as a *user-visible affordance* instead of a settings dialog or a system-prompt directive.
- **Distribution as feature**: brew tap + scoop + choco + AUR + nix + mise + curl + npm + desktop installers all maintained, before opening any provider abstractions. Lowers "I can't try it" friction far more than docs do.
- **22+ language READMEs**: organized, not auto-translated. Community translation surface is treated as a product feature, not a documentation afterthought.
- **`dev` as default branch**: explicit separation of "what's integrating" vs "what's released" — discipline rare for solo or small-team projects.
- **Sessions as first-class, time-travelable objects (2026 Q2)**: snapshot / **revert** (#33226) / **fork** / **move** (#30640) / switch-model mid-session. Treats a conversation as a versioned artifact with an undo, not an append-only log — directly stealable for any agent harness.
- **CLI → platform via a unified HTTP API**: everything (event stream, tui, pty, session mutations) bridged onto one HTTP API (`httpapi`, 28 commits), which is what lets a single core feed CLI + web + desktop + editor front-ends. The form-factor lesson: build the API boundary first, then grow front-ends against it.
- **Growing its own skill + command registry** (#30617 / #30624) and built-in skills (`opencode-meta`) — opencode is "superpowers-ifying" itself, converging with [[superpowers]] (which in turn supports opencode as a *harness*).
- See the full quarter analysis: [Major changes 2026 Feb→Jun](notes/recent-changes-2026-q2.md).

## Open Questions
- **Provider abstraction**: which LLMs are supported, and how is each provider wired (transport, auth, capabilities, capability negotiation)?
- **Skill / plugin system**: now confirmed opencode has its own skill registry + command registry (2026 Q2). Open: how do file-loaded agents, the skill registry, and the `customize-opencode` skill compose? How close is it to Claude Code / superpowers skill semantics?
- **What is "v2" exactly?** A parallel SDK + Effect runtime is landing (`packages/sdk/js/src/v2/`). Open: is v1 being deprecated, or will both ship long-term? What forces the cutover?
- **ACP-next**: how does the Agent Client Protocol layer (event routing, session state, directory snapshot) map to editor integrations (Zed-style)? Is it the desktop/web transport too, or separate from `httpapi`?
- **Compatibility with Claude Code skills**: gstack supports `--host opencode`, suggesting at least partial skill portability — how complete is that compatibility? What's the conversion layer?
- **Anomaly Co. provenance**: is `anomalyco/opencode` the canonical fork, or did the project migrate organizations? Worth understanding the org / governance.
- **dev → main promotion**: what's the release discipline that gates a `dev` commit into a published version?
- **`plan` agent permission model**: how is "denies file edits" enforced — tool filtering at registration, or runtime gate?

## Map
- [Major changes 2026 Feb→Jun](notes/recent-changes-2026-q2.md) — the five concurrent architectural rewrites (v2 runtime, HTTP API, desktop, ACP-next, skill/command registry), with commit evidence.
- Knowledge graph (codegraph, live): `cli-tools/opencode/.codegraph/` — 2,852 files / 50,209 nodes / 120,333 edges. Query via the codegraph MCP (`codegraph_explore`, `codegraph_search`). Rebuild with `codegraph init cli-tools/opencode`.
- Upstream: https://github.com/anomalyco/opencode
- Docs: https://opencode.ai/docs
- Compare with: Claude Code (the system being competed with — Anthropic, not tracked here), [[nano-claude]] (minimal reimpl approach to the same problem)
