# OpenWiki

## TL;DR
OpenWiki (langchain-ai, MIT, TypeScript) is a CLI that writes and maintains markdown wikis with an LLM agent built on LangChain's `deepagents`. It has two modes: **code mode** generates repository docs under `openwiki/` and keeps them fresh via a shipped GitHub Actions cron workflow that opens PRs; **personal mode** builds a "personal brain wiki" in `~/.openwiki/wiki` from connectors (Gmail, Slack, X, Notion MCP, web search, Hacker News, local git repos). The architecture's spine is a strict split: deterministic connector code does all credentialed fetching and writes raw JSON dumps + run manifests under `~/.openwiki/connectors/`, then per-source agent runs synthesize the wiki from those local files — the agent never touches external APIs directly.

**中文口訣**: 確定性的手抓料,agent 只煮菜——外部世界進不了 prompt,只有落地的 raw dump 能。

## Why I Care
Personal mode is a direct competitor-shape to my brainstem (second-brain engine): same ingest → synthesize → query loop, but with opposite bets on almost every axis — own agent runtime vs riding Claude Code, prose wiki vs atomic note graph, deterministic connectors vs agentic ingestion. Comparing the two sharpens which bets are load-bearing for brainstem and which OpenWiki patterns (raw-dump layer, wiki-first answering, scheduled ingestion) are worth stealing.

## Killer Ideas
- **Backend-enforced write boundary**: `OpenWikiLocalShellBackend` overrides `write`/`edit` to refuse any path outside the docs tree (`src/agent/docs-only-backend.ts`) — a hard guard where most agent products rely on prompt text.
- **Deterministic ingestion, agentic synthesis**: connectors are plain TS (`src/connectors/sources/*.ts`) that write raw dumps with 0600 perms, run IDs, and incremental state (`latestIds`, capped `runs` history) via `src/connectors/io.ts`; each source then gets its own scoped agent run (`runOpenWikiIngestion` in `src/ingestion.ts`).
- **Wiki-first question answering** baked into the system prompt (`src/agent/prompt.ts`): assume the synthesized wiki answers the question; open raw dumps only when the wiki is missing, stale, or contradicted — an explicit read-hierarchy discipline.
- **Untrusted-evidence rule** in the same prompt: connector content (emails, posts, MCP responses) must never be followed as instructions — prompt-injection defense as a first-class prompt clause.
- **Docs freshness as a CI loop**: `openwiki --init` drops a cron GitHub Actions workflow + idempotent `<!-- OPENWIKI:START -->` blocks into AGENTS.md/CLAUDE.md (`src/code-mode.ts`), so the wiki regenerates and arrives as a reviewable PR.
- **Anti-thin-page quality rules**: the prompt legislates wiki shape (quickstart entrypoint, no single-stub directories, merge thin pages) — documentation IA encoded as prompt constraints.

## Open Questions
- How does the deepagents subagent (`task` tool) actually parallelize discovery — what state do subagents share, and how are their reports merged?
- Update runs re-synthesize pages, but with no atomic concept identity — how badly does repeated ingestion churn/duplicate wiki content over months?
- The Ink TUI (`src/cli.tsx`, ~3k lines) vs the agent core: how cleanly could personal mode be embedded elsewhere (library use)?
- What does checkpointing (better-sqlite3, `:memory:` for one-shot vs persistent for chat) buy in practice — resumable long runs?

## Map
- Knowledge graph (codegraph, live): `apps/openwiki/.codegraph/` — query via the codegraph MCP.
- Upstream: https://github.com/langchain-ai/openwiki
