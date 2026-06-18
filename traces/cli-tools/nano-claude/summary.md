# nano-claude

## TL;DR
A minimal reimplementation of **Claude Code** in **~2,000 lines of TypeScript across 19 files** (vs Claude Code's 512,685 LOC across 1,902 files). **7 tools** (Bash, Read, Write, Edit, Glob, Grep, Agent) vs Claude Code's ~50. Node.js + readline UI instead of Bun + React+Ink. Every milestone is a separate git tag (`v0` chatbot → `v8` CLI args), explicitly designed as a *learning artifact* with a dedicated `docs/LEARNING_GUIDE.md` walking through each version. The thesis it proves by construction: **the model IS the agent; code is just a harness that wires tools + context + memory into a streaming API loop**.

**中文口訣**: Model 即 agent，code 只是把 tools + context + memory 接到 streaming API 的薄殼；v0 → v8 一版一版疊出來，每個概念都必要、且都能單獨讀懂。

## Why I Care
The fastest path to understanding a complex system is a minimal-viable reimplementation. nano-claude's `git checkout v0..v8` walkthrough is the kind of pedagogical artifact that *should* exist for every interesting agent system. For this catalog's mission (*靠 source code 學東西*), **nano-claude is the Rosetta Stone** for the Claude-Code-style CLI agent. The fact that it fits in one context window (11K words) means I can ask the graph "what's the system prompt recipe" and get a complete answer, not a sample.

## Killer Ideas
- **Versioned learning roadmap** — each git tag is an independently-runnable milestone with a documented "key concept":
  - `v0` chatbot baseline — streaming REPL, no tools (~80 LOC)
  - `v1` MVP — REPL + 6 tools + streaming, *the agentic tool-use loop* (~900 LOC)
  - `v2` markdown rendering, *two-pass: stream raw then ANSI-erase and re-render* (~1020)
  - `v3` permission confirmation + bash safety, *two-tier risk classification (safe/write/destructive)* (~1140)
  - `v4` session persistence + resume, *sessions saved as raw API message array — no parsing on resume* (~1320)
  - `v5` smart conversation compaction, *75% of 200K threshold → keep last 2 + summarize rest + ack* (~1450)
  - `v6` multi-level CLAUDE.md + memory, *system prompt = CORE_PROMPT + env + git + CLAUDE.md + memories* (~1630)
  - `v7` sub-agent tool, *isolated, read-only (Read/Glob/Grep only, no Bash/Write/Edit), up to 20 turns* (~1780)
  - `v8` CLI arguments + non-interactive mode, *print mode auto-allows permissions and exits when model done* (~2000)
- **7 tools is enough** — assertion-by-construction that an agentic coding CLI only needs Bash, Read, Write, Edit, Glob, Grep, Agent. Everything else Claude Code ships is convenience or specialization, not load-bearing. Graph confirms with hyperedge "All Tool implementations" (cohesion 0.39).
- **System prompt is a concatenation, not a template** — `buildSystemPrompt()` in `src/prompt.ts` literally concatenates `CORE_PROMPT + env + git context + multi-level CLAUDE.md + persistent memories`. Five sources, ordered, no Jinja, no escaping. That's the recipe.
- **Sub-agent security boundary is a constant, not a policy engine** — sub-agents get a *literal whitelist* of Read/Glob/Grep tools; Bash/Write/Edit are simply not in their toolset. Permissions-as-architecture rather than permissions-as-runtime-check.
- **Sessions = raw API message array on disk** — no schema migration, no parsing layer. `loadSession()` reads JSON and the API loop continues. Hottest pattern in the repo: zero ceremony, zero coupling to internal types.
- **Two-pass markdown rendering** is its own community in the graph (cohesion 1.0 — perfectly self-contained). Stream raw text into terminal, then ANSI-erase the lines and re-render with markdown formatting. Avoids a streaming-markdown parser entirely.
- **Permission confirmation, not prevention** — design principle: don't try to block dangerous actions, just make the user explicitly choose them. `classifyBashRisk()` regex-matches `DESTRUCTIVE_PATTERNS` and `WRITE_PATTERNS`; everything else is "safe".
- **Slash commands as parallel surface** — `/help`, `/cost`, `/clear`, `/compact`, `/model`, `/sessions`, `/resume`, `/remember`, `/forget`, `/memory`. Useful baseline for what's *core* slash UX in a Claude-style CLI. `handleCommand()` is one of the top god nodes (9 edges).
- **Cohesion as design signal** — `API & Main Loop` and `Core Functions` clusters score 0.09 (most cross-cutting); pure additions like markdown rendering (1.0) and CLI args (0.67) are self-contained. The numbers visually confirm a clean modular layering.

## Open Questions
- What got *cut* from Claude Code's ~50 tools that nano-claude proves unnecessary? (MCP, NotebookEdit, IDE integrations, WebFetch/WebSearch, TodoWrite, …) Worth tabulating side-by-side.
- **55 isolated nodes** in the graph (mostly constants like `RiskLevel`, `DESTRUCTIVE_PATTERNS`, `SESSION_DIR`) — are they truly orphan, or just type definitions whose usage isn't captured in semantic extraction?
- v8's `--dangerously-skip-permissions` flag — same semantics as Claude Code's, or simpler?
- Multi-level CLAUDE.md (v6): how is hierarchy walked? Does it mirror Claude Code's exact algorithm (cwd → parents → ~/.claude/CLAUDE.md), or simplified? Graph hints at `~/.claude + project root + .claude + cwd (concatenated, gitignore-style)` — needs verification.
- Sub-agent (v7): 20-turn cap is documented, but what happens on cap exceedance — error, silent truncate, summarize-and-return? Read the code.
- Streaming wrapper (`api.ts`) — is it just `@anthropic-ai/sdk` defaults, or are there non-obvious choices around tool-call deltas / interrupted streams / token usage tracking?

## Map
- Knowledge graph (codegraph, live): `cli-tools/nano-claude/.codegraph/` — 18 files / 165 nodes / 317 edges. God nodes worth a look: `main`, `runConversationLoop`, `Tool`, `handleCommand`, `buildSystemPrompt`. Query via the codegraph MCP (`codegraph_explore`, `codegraph_search`). Rebuild with `codegraph init cli-tools/nano-claude`.
- Upstream: <https://github.com/dox012/nano-claude> · pinned at `21ee5c6`
- Upstream learning guide: [`docs/LEARNING_GUIDE.md`](../../../cli-tools/nano-claude/docs/LEARNING_GUIDE.md) (and `_CN.md` Chinese version)
- Compare with: Claude Code (the system being minimized — Anthropic, not tracked here), [[opencode]] (different approach to the same problem)
- Deep dives (planned, not yet written):
  - `notes/system-prompt-recipe.md` — trace `buildSystemPrompt()` line-by-line; compare to Claude Code's
  - `notes/v0-to-v8-walkthrough.md` — what's the minimum diff between each version, with `git diff vN..vN+1` excerpts
  - `notes/cut-from-claude-code.md` — what's NOT in the 7-tool baseline that Claude Code ships
