# opencode

## TL;DR
Open-source agentic coding CLI in the Claude Code space. Single-binary install (`brew install anomalyco/tap/opencode`, curl script, scoop, choco, AUR, mise, nix...) plus desktop apps for mac/Windows/Linux. Ships **two built-in agents** — `build` (full-access for development) and `plan` (read-only for exploration, denies file edits, asks before bash) — switchable with **Tab**. Internal `general` subagent for multistep search. MIT-licensed, defaults to the `dev` branch, very active (v1.15.5 released 2026-05-18).

**中文口訣**: Claude Code 之外的 OSS 替代品 — build/plan 雙 agent 一鍵切換，內建多 provider。

## Why I Care
Same problem space as [[claude-code]], different design constraints. The interesting reads are where opencode *deliberately diverges*: the explicit build/plan agent split (vs Claude Code's implicit "ask before destructive" pattern), the desktop-app form factor, and the provider abstraction. Provides a clean diff against Anthropic's design choices.

## Killer Ideas
- **Two-agent mode toggle (`build` vs `plan`)**, **Tab-switchable**: plan-mode denies edits and confirms bash by default. Surfaces the safety/exploration tradeoff as a *user-visible affordance* instead of a settings dialog or a system-prompt directive.
- **Distribution as feature**: brew tap + scoop + choco + AUR + nix + mise + curl + npm + desktop installers all maintained, before opening any provider abstractions. Lowers "I can't try it" friction far more than docs do.
- **22+ language READMEs**: organized, not auto-translated. Community translation surface is treated as a product feature, not a documentation afterthought.
- **`dev` as default branch**: explicit separation of "what's integrating" vs "what's released" — discipline rare for solo or small-team projects.

## Open Questions
- **Provider abstraction**: which LLMs are supported, and how is each provider wired (transport, auth, capabilities, capability negotiation)?
- **Skill / plugin system**: does opencode have a Claude-Code-equivalent extensibility model, or its own design?
- **Compatibility with Claude Code skills**: gstack supports `--host opencode`, suggesting at least partial skill portability — how complete is that compatibility? What's the conversion layer?
- **Anomaly Co. provenance**: is `anomalyco/opencode` the canonical fork, or did the project migrate organizations? Worth understanding the org / governance.
- **dev → main promotion**: what's the release discipline that gates a `dev` commit into a published version?
- **`plan` agent permission model**: how is "denies file edits" enforced — tool filtering at registration, or runtime gate?

## Map
- Long-form deep dives: not yet written. As they land they will be linked here from `notes/<topic>.md`.
- Knowledge graph: `graphify-out/cli-tools/opencode/` *(not yet generated — run `/graphify cli-tools/opencode`)*
- Upstream: https://github.com/anomalyco/opencode
- Docs: https://opencode.ai/docs
- Compare with: [[claude-code]] (the system being competed with), [[nano-claude]] (minimal reimpl approach to the same problem)
