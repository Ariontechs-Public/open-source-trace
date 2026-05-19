# Superpowers

## TL;DR
Superpowers is a skill-based agent layer shipped as a Claude Code plugin (and adapted to Codex, Copilot, Gemini, OpenCode). A skill is a single markdown file with YAML frontmatter; the agent loads every skill's `description:` at session start, then invokes a skill by name when its description matches the task. Skills compose into an explicit **7-step pipeline** — *brainstorm → worktree → plan → subagent-driven dev → TDD → review → finish* — which the graph confirms is the project's spine (highest-cohesion hyperedge, 1.0 EXTRACTED). Two skills act as the cross-cutting hubs (`systematic-debugging`, `subagent-driven-development`); the rest fan off them.

**中文口訣**: Skill 即工具，描述即路由；流程在前、實作在後；7 步主動脈，跨 harness 通用。

## Why I Care
I'm trying to make AI agents *reliable at workflow discipline* — TDD, brainstorming-before-coding, design-before-implementation. Superpowers' answer is **"externalize each workflow as a skill, let the model route by description"** — a pattern that's directly portable to anything I build with Claude/Gemini/OpenAI tooling. The 7-step pipeline is also a useful blueprint for any agentic system that needs to gate creative work behind structured review.

## Killer Ideas
- **Skill = `.md` + frontmatter description, nothing more.** The model routes by `description:`. See any [`skills/<name>/SKILL.md`](../../../agents/superpowers/skills/) — and the `using-superpowers` skill that establishes priority ordering.
- **The 7-step pipeline as an explicit hyperedge** — `brainstorming → using-git-worktrees → writing-plans → subagent-driven-development → test-driven-development → requesting-code-review → finishing-a-development-branch`. This is the project's spine; everything else is a specialization or support skill. (Graph: see Hyperedge "Core Basic Workflow Pipeline".)
- **HARD-GATE textual contracts** — e.g. brainstorming's *"no implementation until design approved"*. Plain prose the model actually respects. (See `skills/brainstorming/SKILL.md`.)
- **DOT/Graphviz flowcharts embedded in SKILL.md** parse as executable procedural intent to the model. Used heavily in `using-superpowers` and `systematic-debugging`.
- **Two-stage code review** decomposed: `spec-reviewer-prompt` (compliance) then `code-quality-reviewer-prompt` (quality). Both run as subagents inside SDD — see `skills/subagent-driven-development/`.
- **Defense-in-Depth Four Layers** for debugging (entry / business / environment / debug) — a fully named anti-checklist for "where does the bug actually live."
- **Acceptance-test contributor gate** — `AGENTS.md` documents a *94% PR rejection rate* for AI contributors, and ships a reference task ("make a React todo list") that contributors must pass before opening PRs. Data-driven gating.
- **Cross-harness portability** — same skills, with `references/{copilot,codex,gemini}-tools.md` mapping tool names per platform. Skill stays harness-agnostic.
- **TDD-for-documentation** — `writing-skills` skill applies red/green/refactor to writing skills themselves. Meta but coherent.
- **Brainstorm server evolution**: visual-companion built → refactored non-blocking → zero-dep rewrite. The graph captured the arc as a hyperedge — useful pattern: ship simple, refactor for concurrency, then strip deps.

## Open Questions
- **Skill discovery across plugins.** How does Claude Code merge skill catalogs from multiple installed plugins? Is there name-collision handling, or last-write-wins? (Graph shows `using-superpowers` references plugin paths but the resolution logic is in CC itself, not in this repo.)
- **Loading cost vs scale.** Every skill's `description:` is loaded into every conversation. The repo has ~15 skills today; what's the breaking point at 100+? Is there lazy/triggered loading anywhere?
- **Inheritance under subagent dispatch.** When SDD spawns implementer + reviewer subagents, do they each re-route through `using-superpowers`, or inherit the parent's loaded skill set? Token implication is large.
- **Rigid vs flexible** labels — formal flag or social convention? Couldn't find a schema field; looks like prose-only.
- **Brainstorm-server orphan code?** The zero-dep WebSocket server (`scripts/server.cjs`) has 4 surprisingly direct test→implementation calls (per graph). Worth a deep dive into whether it's the recommended local UX or a deprecated artifact.

## Map
- Knowledge graph: [`graphify-out/agents/superpowers/`](../../../graphify-out/agents/superpowers/graphify-out/) — 438 nodes / 492 edges / 37 communities. Open `graph.html` in browser.
- Audit report: [`graphify-out/agents/superpowers/graphify-out/GRAPH_REPORT.md`](../../../graphify-out/agents/superpowers/graphify-out/GRAPH_REPORT.md) — god nodes, hyperedges, suggested questions.
- Upstream: <https://github.com/obra/superpowers> · pinned at `v5.1.0`.
- Deep dives (planned, not yet written):
  - `notes/7-step-pipeline.md` — trace each skill in the main artery, what it gates and what artifacts it produces
  - `notes/skill-anatomy.md` — frontmatter conventions, HARD-GATE patterns, DOT-as-flowchart examples
  - `notes/cross-harness-adapter.md` — how the same skills run on CC vs Codex vs Gemini vs OpenCode
