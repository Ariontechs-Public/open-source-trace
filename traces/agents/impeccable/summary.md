# Impeccable

## TL;DR
Impeccable is "the design language that makes your AI harness better at design." Where UI UX Pro Max hands the model a *menu* of styles, impeccable takes the opposite bet: it ships **one deeply-opinionated design system** — *Neo Kinpaku*, kinpaku-gold + verdigris-patina on warm lacquer-black, expressed entirely in OKLCH tokens — and teaches the harness to apply *that*. It's more than prose: `cli/engine/` contains a real **anti-pattern detector that runs in a headless browser** (`detect-antipatterns-browser.js`, ~212 KB) to audit rendered UI, plus a `design-system.mjs` generator. The skill (`skill/SKILL.src.md`) is distributed to ~14 AI harnesses — and is the very `impeccable` skill loaded in this Claude Code session.

**中文口訣**: 不給菜單給信仰 — 一套 Neo Kinpaku 設計語言;OKLCH token 當真理,瀏覽器引擎抓反模式。

## Why I Care
This is the strongest example I've seen of **moving design quality from "advice" to "enforced system + automated audit"**. Two ideas are directly stealable: (1) encode a design language as a single token source-of-truth (CSS) with a portable export (frontmatter), and (2) close the loop with a browser-based linter that *checks rendered output* against the language — not just generates and hopes. That audit-the-render loop is exactly what my own design-generation tooling lacks.

## Killer Ideas
- **Conviction over catalog.** One named design language (Neo Kinpaku) with brand anchors, surface ramps, and a gold/patina accent system. Restraint as a feature. Contrast with [[ui-ux-pro-max-skill]]'s menu approach.
- **OKLCH tokens as source of truth.** `site/styles/kinpaku-tokens.css` is canonical; `DESIGN.md` frontmatter mirrors it verbatim as a portable export. Perceptually-uniform color space chosen deliberately.
- **Audit-the-render loop.** `cli/engine/detect-antipatterns-browser.js` runs in a real browser to catch design anti-patterns in *rendered* UI — generation + verification, not generation alone.
- **Engine, not just a prompt.** `cli/engine/` is structured (`rules/`, `engines/`, `registry/`, `profile/`, `browser/`) — a genuine design-linting toolchain wrapped by the skill.
- **Extreme cross-harness fan-out.** Per-harness directories for Claude, Codex, Cursor, Gemini, Pi, Kiro, OpenCode, Trae, Qoder, Rovodev, and more — the widest distribution surface in this repo.

## Open Questions
- **How does the browser audit feed back into generation?** Is `detect-antipatterns` a one-shot lint, or a closed loop where findings drive a re-design pass?
- **Token enforcement mechanism** — does the skill hard-reject off-palette colors, or just nudge? Where's the gate?
- **DESIGN.md ↔ kinpaku-tokens.css dual-write** is maintained by hand (the file says so). Is there a check that keeps them in sync, or is drift a known risk?
- **`functions/` + `site/`** suggest a hosted component — is impeccable also a live web service, or purely a local skill?
- **Generality of Neo Kinpaku** — a single dark-gold language is gorgeous but narrow. How does it adapt when a project needs a light, playful, or minimal brand?

## Map
- Knowledge graph (codegraph, live): `agents/impeccable/.codegraph/` — 1,181 files / 24,945 nodes / 59,752 edges. Query via the codegraph MCP (`codegraph_explore`, `codegraph_search`).
- Design language: `agents/impeccable/DESIGN.md` + `agents/impeccable/site/styles/kinpaku-tokens.css`
- Engine: `agents/impeccable/cli/engine/` (`design-system.mjs`, `detect-antipatterns-browser.js`, `rules/`)
- Skill source: `agents/impeccable/skill/SKILL.src.md`
- Upstream: <https://github.com/pbakaus/impeccable> · pinned at `cli-v3.0.3-6-g1c897a09`
