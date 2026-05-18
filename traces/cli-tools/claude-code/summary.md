# Claude Code

## TL;DR
Anthropic's official agentic coding CLI: terminal-native, codebase-aware, handles git workflows via natural language. Distributed via **native installers** (curl script, Homebrew cask, Windows installers) — the original `npm install -g @anthropic-ai/claude-code` path is now explicitly deprecated. Ships with a plugin system (first-party plugins live in this repo under `plugins/`) and integrates with IDEs and GitHub (`@claude` mentions in PRs/issues).

**中文口訣**: AI agent 跑在 terminal 裡，當代許多 OSS 都圍著它做生態。

## Why I Care
**Baseline.** Almost every other project in this catalog ([[superpowers]], [[gstack]], [[opencode]], [[nano-claude]]) either plugs into Claude Code, mimics it, or competes with it. Understanding Claude Code's architecture is the prerequisite for understanding what's *accidental* vs *essential* in the other CLI agents.

## Killer Ideas
*First pass limited to the public README + install docs (the substantive architecture lives at https://code.claude.com/docs/en/overview, not in-repo). Killer ideas will land here as I read the actual source.*

A first observation worth recording: **the README is deliberately thin.** No architecture diagram, no module map, no skill/plugin internals — just "how do I install it" + a pointer to docs.claude.com. That's a choice. The signal to learners is *"the docs site is the source of truth, the repo is the artifact."* That's the opposite of nano-claude's *"read the source in version order."*

## Open Questions
- **Plugin / skill loader architecture**: how does the runtime discover and route to plugins/skills? Filesystem scan? Manifest? When are skill descriptions loaded into context?
- **Memory / context compression in long sessions**: where in the source does compaction live, and what triggers it? Token threshold, turn count, manual?
- **MCP transport details**: how does the SDK abstract stdio vs SSE vs HTTP MCP servers? Where's the auth/handshake code?
- **Hook system**: which lifecycle events are exposed (PreToolUse, PostToolUse, Stop, …), threading/exec model, error containment.
- **IDE bridge**: how is VS Code / JetBrains integration wired? Native binary speaking a protocol, or pure CLI shell-out?
- **npm deprecation rationale**: what failed about npm-based distribution that drove the curl/brew pivot? (Permissions? Update reliability? Binary size?)
- **Auto-update + version-pinning policy**: how does the curl installer handle version locking?

## Map
- Long-form deep dives: not yet written. As they land they will be linked here from `notes/<topic>.md`.
- Knowledge graph: `graphify-out/cli-tools/claude-code/` *(not yet generated — run `/graphify cli-tools/claude-code`)*
- Upstream: https://github.com/anthropics/claude-code
- Official docs: https://code.claude.com/docs/en/overview
- Plugins reference: `cli-tools/claude-code/plugins/README.md`
- Compare with: [[opencode]] (open-source competitor), [[nano-claude]] (minimal reimpl)
