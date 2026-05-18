# gstack

## TL;DR
Garry Tan's personal Claude Code skill bundle: **23 opinionated slash-command "specialists"** (CEO, Designer, Eng Manager, Reviewer, QA Lead, SRE, Chief Security Officer, Technical Writer, Performance Engineer, etc.) plus 8 power tools, all Markdown, MIT-licensed. Models a software factory as a workflow pipeline — **Think → Plan → Build → Review → Test → Ship → Reflect** — where each skill writes a document the next skill reads. Works across 10+ AI agents (Claude Code, Codex, opencode, Cursor, OpenClaw, Hermes, GBrain, Kiro, Factory, Slate) via per-host adapter configs.

**中文口訣**: 用 23 個專家代替一整個工程團隊 — 角色綁工作流，文件接力。

## Why I Care
Direct comparison point against [[superpowers]]. Both externalize workflows as `.md` skills, but Superpowers ships **primitive** skills (TDD, debugging, brainstorming) for the user to compose, while gstack ships **role-bundled** skills (a CEO who challenges scope, a Designer who fixes slop) that compose themselves through the design-doc handoff. Worth understanding which lens generalizes — and whether 23 personas is *the* right abstraction or just *an* abstraction.

## Killer Ideas
- **Document-passing as skill orchestration**: `/office-hours` writes a design doc that `/plan-ceo-review` reads, which `/plan-eng-review` extends, which `/qa` picks up. The skill graph is implicit in what each step writes to disk — no explicit orchestration layer. (README "The sprint" + `autoplan/`)
- **Persona-bundled skills > primitive skills (for non-experts)**: each slash command is a specialist with attitude. Lower friction than "pick the right TDD skill."
- **Host adapters as portability layer**: `hosts/<agent>.ts` per agent; `./setup` auto-detects which agents are installed. Skills themselves are agent-agnostic Markdown.
- **Template-generated SKILL.md**: `.tmpl` is the source of truth, `bun run gen:skill-docs` emits SKILL.md. Lets shared preamble (writing style, jargon glossary) compile in without per-skill duplication.
- **Diff-based eval selection** (`test:evals`): each test declares its file dependencies; only deps-touched tests run. Cost-aware test infra at a tier most projects don't bother with (`~$4/run max` is named as a budget).
- **Release-as-verdict CHANGELOG**: every `## [X.Y.Z]` entry leads with a two-line bold headline and a "numbers that matter" table. Treats release notes as the primary user-facing artifact, not a contributor diary.

## Open Questions
- How does the design-doc handoff *physically* work? Filename convention? Header parsing? A single canonical path? Where in the source is the handoff contract enforced?
- Per-skill memory: where does `/learn` write learnings, and how do downstream skills read them in subsequent sessions?
- The "host adapter" abstraction — how thin is it actually? What breaks when a non-Claude agent runs a gstack skill?
- 23 slash commands is a *lot*. How does the user actually pick one? Is there a meta-skill that routes (like Superpowers' `using-superpowers`)?
- Token/cost discipline: gstack ships paid evals as part of the workflow. What's the actual per-PR cost in production use, and how does that shape skill design?
- The Andrej Karpathy / "810× LOC" framing in the README — is it the actual on-ramp for users, or aspirational marketing? Does the social positioning materially shape adoption?

## Map
- Long-form deep dives: not yet written. As they land they will be linked here from `notes/<topic>.md`.
- Knowledge graph: `graphify-out/agents/gstack/` *(not yet generated — run `/graphify agents/gstack`)*
- Upstream: https://github.com/garrytan/gstack
- Compare with: [[superpowers]] (different skill granularity philosophy), [[claude-code]] (the host)
