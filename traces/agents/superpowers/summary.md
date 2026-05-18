# Superpowers

## TL;DR
Superpowers is a skill-based agent layer that ships as a Claude Code plugin. Skills are individual markdown files with YAML frontmatter; the agent loads all skill descriptions at conversation start and invokes a skill by name when its description matches the task. Skills are categorized as either **rigid** (TDD, debugging — follow exactly) or **flexible** (patterns — adapt), and they declare an explicit priority order over the default system prompt.

**中文口訣**: Skill 即工具，描述即路由；流程在前、實作在後。

## Why I Care
I'm trying to figure out how to make AI agents *reliable at workflow discipline* — TDD, brainstorming-before-coding, design-before-implementation. Superpowers' answer is "externalize each workflow as a skill, let the agent pick by description." That's a pattern worth stealing for any agentic system I build.

## Killer Ideas
- **Skill = `.md` + frontmatter description**, nothing more. The model decides relevance from the `description:` field. See any `skills/<name>/SKILL.md`.
- **HARD-GATE blocks** inside skills (e.g., brainstorming's "no implementation until design approved"). Simple textual contract that the model actually respects.
- **DOT graphviz diagrams in skills** for flow control. The model parses them as procedural intent.
- **Skill priority order** (process > implementation) baked into the meta skill `using-superpowers` rather than per-skill — single point of override.
- **Subagent dispatch** as a first-class concept, with worktree isolation for parallel work.

## Open Questions
- How are skills *discovered* across plugins? Manifest, or filesystem scan?
- What's the token cost of loading every skill description into every conversation? Does it scale to 100+ skills?
- How does the skill-priority system play with multi-agent dispatch — does each subagent re-evaluate, or inherit?
- Are "rigid vs flexible" skill labels formal, or social convention?

## Map
- Long-form deep dives: not yet written. As they land they will be linked here from `notes/<topic>.md`.
- Knowledge graph: `graphify-out/agents/superpowers/` *(not yet generated — run `/graphify agents/superpowers`)*
- Upstream: https://github.com/obra/superpowers
