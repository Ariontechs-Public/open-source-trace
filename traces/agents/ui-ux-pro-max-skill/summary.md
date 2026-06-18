# UI UX Pro Max

## TL;DR
UI UX Pro Max is an **AI skill that ships design taste as data**. Rather than asking the model to invent a design system from scratch, it curates a structured knowledge base — 67 UI styles, 161 color palettes, 57 font pairings, 99 UX guidelines, 25 chart types — stored as data files under `src/ui-ux-pro-max/data/`, and pairs it with a reasoning prompt that maps a project brief to a concrete `RECOMMENDED DESIGN SYSTEM` block (pattern + style + colors + typography + effects + anti-patterns). A `uipro-cli` (TypeScript/Bun, npm `uipro-cli`) installs the skill into 18+ AI harnesses (Claude, Cursor, Copilot, Codex, Gemini, Windsurf, …).

**中文口訣**: 設計品味當資料餵,推理引擎填模板;一條 CLI 散佈 18 家 harness。

## Why I Care
I keep hitting the "AI picks generic purple-gradient UI" failure mode. This repo's answer — **constrain the model with a curated palette/font/style corpus and force a fixed output template** — is directly portable to any design-generation feature I build. It's also a clean example of the same cross-harness distribution pattern superpowers uses, but for a *knowledge* skill rather than a *workflow* skill.

## Killer Ideas
- **Taste-as-data.** Palettes, fonts, styles, UX rules live as enumerable data files (`src/ui-ux-pro-max/data/`), not prose. The model selects + composes rather than hallucinates. Worth stealing for any "make the LLM tasteful" problem.
- **Fixed output contract.** The recommendation is a rigid ASCII template (PATTERN / STYLE / COLORS / TYPOGRAPHY / EFFECTS / AVOID). Deterministic shape → easy to parse downstream and hard for the model to wander.
- **Explicit anti-patterns.** Every recommendation names what to *avoid* ("bright neon, harsh animations, AI purple/pink gradients") — negative constraints, not just positive ones.
- **CLI-as-distribution.** `uipro-cli init --ai <platform>` templatizes install across 18+ harnesses (`skill.json` `platforms` array). Same playbook as superpowers' per-harness adapters.

## Open Questions
- **How is the data corpus actually injected?** Full dump into context, or retrieved on demand? At 67 styles × 161 palettes the token cost of naive injection is large — is there a selection step before the model sees it?
- **Reasoning engine location.** Is the "Design System Generator" a prompt template, a Python script (`src/.../scripts/`), or both? Need to read the engine to know how much is deterministic vs LLM.
- **Data provenance / licensing** of the 161 palettes and 57 font pairings — curated by hand, scraped, or generated?
- **Overlap with impeccable** — both are "make the harness better at design" skills. Where do their philosophies diverge (curated catalog vs single opinionated design language)?

## Map
- Knowledge graph (codegraph, live): `agents/ui-ux-pro-max-skill/.codegraph/` — 47 files / 772 nodes / 1437 edges. Query via the codegraph MCP (`codegraph_explore`, `codegraph_search`).
- Skill manifest: `agents/ui-ux-pro-max-skill/skill.json`
- Data corpus: `agents/ui-ux-pro-max-skill/src/ui-ux-pro-max/data/`
- Upstream: <https://github.com/nextlevelbuilder/ui-ux-pro-max-skill> · pinned at `v2.5.0-22-gb7e3af8`
