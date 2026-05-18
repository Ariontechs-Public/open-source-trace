# nano-claude

## TL;DR
A minimal reimplementation of [[claude-code]] in **~2,000 lines of TypeScript across 19 files** (vs Claude Code's 512,685 LOC across 1,902 files). **7 tools** (Bash, Read, Write, Edit, Glob, Grep, Agent) vs Claude Code's ~50. Built on Node.js with a readline UI instead of Bun + React+Ink. Every milestone is a separate git tag (`v0` chatbot → `v8` CLI args), explicitly designed as a *learning artifact* with a dedicated `docs/LEARNING_GUIDE.md` walking through each version.

**中文口訣**: 從 v0 到 v8 一版一版疊出 Claude Code — 最少程式碼證明每個概念都必要。

## Why I Care
The fastest path to understanding a complex system is a minimal-viable reimplementation. nano-claude's `git checkout v0..v8` walkthrough is the kind of pedagogical artifact that *should* exist for every interesting agent system. For this catalog's mission (*靠 source code 學東西*), **nano-claude is the Rosetta Stone** for the Claude-Code-style CLI agent.

## Killer Ideas
- **Versioned learning roadmap**: each git tag is an independently-runnable milestone with a documented "key concept":
  - `v0` chatbot baseline — streaming REPL, no tools (~80 LOC)
  - `v1` MVP — REPL + 6 tools + streaming, *the agentic tool-use loop* (~900 LOC)
  - `v2` markdown rendering (~1020)
  - `v3` permission confirmation + bash safety, *tool risk classification* (~1140)
  - `v4` session persistence + resume, *state management* (~1320)
  - `v5` smart conversation compaction, *context window management* (~1450)
  - `v6` multi-level CLAUDE.md + memory, *project awareness* (~1630)
  - `v7` sub-agent tool, *multi-agent orchestration* (~1780)
  - `v8` CLI arguments + non-interactive mode, *scripting and automation* (~2000)
- **7 tools is enough**: assertion-by-construction that an agentic coding CLI only needs Bash, Read, Write, Edit, Glob, Grep, Agent. Everything else Claude Code ships is convenience or specialization, not load-bearing.
- **Permission risk classification baked in (v3)**: Bash is "classified per command"; Read/Glob/Grep/Agent are "safe"; Write/Edit are "write". Demonstrates the minimum viable permission model.
- **Smart compaction as its own milestone (v5)**: rather than bundling compaction into the chat loop, it gets its own ~130 LOC version. Highlights that compaction is a discrete, isolatable concept.
- **Sub-agent as the 7th tool (v7)**: spawning a sub-agent is just another tool, not a separate dispatch system. ~150 LOC for the orchestration primitive.
- **Slash commands as a parallel surface**: `/help`, `/cost`, `/clear`, `/compact`, `/model`, `/sessions`, `/resume`, `/remember`, `/forget`, `/memory` — useful baseline for what's *core* slash UX in a Claude-style CLI.

## Open Questions
- What got *cut* from Claude Code's ~50 tools that nano-claude proves unnecessary? (MCP, NotebookEdit variants, IDE integrations, …)
- How does the prompt template differ from Claude Code's? Where is the system prompt assembled, what does it contain?
- Streaming wrapper (`api.ts`) — is it just `@anthropic-ai/sdk` defaults, or are there non-obvious choices around tool-call deltas?
- Markdown rendering (v2): which library / what's the actual code path? `marked-terminal`, or hand-rolled?
- Multi-level CLAUDE.md (v6): how is hierarchy walked? Does it mirror Claude Code's exact algorithm (cwd → parents → ~/.claude/CLAUDE.md)?
- Sub-agent (v7): is it the same Claude session re-entered with cleared history, or a fresh API call? How is the result returned to the parent?
- v8's `--dangerously-skip-permissions` flag — same semantics as Claude Code's, or simpler?

## Map
- Long-form deep dives: not yet written. As they land they will be linked here from `notes/<topic>.md`.
- Knowledge graph: `graphify-out/cli-tools/nano-claude/` *(not yet generated — run `/graphify cli-tools/nano-claude`)*
- Upstream: https://github.com/dox012/nano-claude
- Learning guide: `cli-tools/nano-claude/docs/LEARNING_GUIDE.md`
- Compare with: [[claude-code]] (the system being minimized), [[opencode]] (different approach to the same problem)
