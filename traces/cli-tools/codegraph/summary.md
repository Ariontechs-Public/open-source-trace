# codegraph

## TL;DR
A local-first code knowledge graph for AI coding agents by Colby McHenry. TypeScript + `web-tree-sitter` (WASM) parse 20+ languages into a SQLite graph (with FTS5 full-text), exposed over MCP as **8 tools** centered on `codegraph_explore` — one call returns the relevant symbols' source plus call paths (including dynamic-dispatch hops) and blast radius, collapsing the agent's grep/glob/Read crawl. Despite the "semantic" branding it does **not** use vector embeddings; the leverage is structure + one-shot explore. Shipped **v1.1.1** (19 releases), ~54k stars, MIT, 100% local. Distinctive extras: per-file staleness banners for live-sync correctness and specialized iOS/React Native/Expo cross-language bridging.

**中文口訣**: 精悍务实的 TS 小刀 — tree-sitter 建图 + FTS,一个 `explore` 收敛爬取;赌「结构图够用」,把 live-sync UX 磨亮。

## Why I Care
This is the more mature, more popular end of the "code graph as MCP" design space, and a direct foil to [[codebase-memory-mcp]]. The interesting read is what it *chooses not to do*: no embeddings, no Cypher, no cross-repo — just a tight structural graph and a single well-shaped `explore` tool. It's also a clean case study in "marketing vs source": the README says "semantic," third-party aggregators say 42 tools / 38 langs / 94% savings, but the actual v1.1.1 source is 8 tools / 20+ langs / 58% fewer calls. Reading the source corrected every secondhand number — exactly this repo's thesis.

## Killer Ideas
- **One-shot `codegraph_explore`**: instead of N granular tools, a single tool takes NL or a bag of symbols and returns verbatim source grouped by file + call paths + blast radius. Collapses redundant interchangeable implementations to signatures so the response is sized to the *answer*, not the file count.
- **Live-sync correctness as UX**: during the debounce window, MCP responses prepend a `⚠️` banner naming still-pending files and telling the agent to `Read` them directly; on (re)connect it runs a `(size, mtime)` + content-hash reconciliation so out-of-band edits (a terminal `git pull`, another editor) get absorbed on the first query. Staleness is surfaced, not hidden.
- **FTS5 over embeddings**: deliberately no vector search — keyword full-text + the structural graph. Cheaper, deterministic, zero model bundling. A bet that structure + exact-name search beats similarity for "where is X / what calls Y".
- **Cross-language bridging for mobile**: synthesizes edges across Swift↔ObjC bridging, RN legacy bridge + TurboModules + Fabric, native→JS event emitters, Expo Modules — flows static tree-sitter extraction stops at.

## Open Questions
- How does `codegraph_explore` rank/select symbols? An open issue notes generic tokens overboost on exact name-match in peripheral results — what's the scoring?
- The shared daemon serializes concurrent tool calls (N clients = N× tail latency, per an open issue) — what's the concurrency model, and is it being fixed?
- Watch-mode resource pressure on macOS + large-DB timeouts (reported in independent eval) — what are the real limits on a big monorepo?
- Where exactly does FTS5 stop being enough? On what query shapes would the lack of embeddings actually hurt vs [[codebase-memory-mcp]]'s `semantic_query`?
- Is the v0.9.x → v1.x tool consolidation (many tools → 8) documented anywhere, and did answer quality change?

## Map
- [Head-to-head vs codebase-memory-mcp](notes/vs-codebase-memory-mcp.md) — full comparison: stack, features, MCP tools, benchmarks, third-party reviews, star growth.
- Knowledge graph (codegraph, live): built 2026-06-25 from `4077ed1` — **283 files / 4,589 nodes / 13,106 edges / 1.5s**. (Indexed in a scratchpad clone; submodule not yet added, so no committed `cli-tools/codegraph/.codegraph/` here. Rebuild with `codegraph init cli-tools/codegraph` once the submodule lands.)
- Upstream: https://github.com/colbymchenry/codegraph
- Docs: https://colbymchenry.github.io/codegraph/
- Compare with: [[codebase-memory-mcp]] (the maximalist C foil), [[opencode]] / [[nano-claude]] (same Claude-Code-adjacent OSS space)
