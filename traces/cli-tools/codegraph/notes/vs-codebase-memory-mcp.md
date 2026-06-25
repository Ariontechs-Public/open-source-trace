# codegraph vs codebase-memory-mcp — head-to-head

> Traced 2026-06-25. Both cloned and indexed locally with `codegraph init`.
> codegraph @ `4077ed1` (v1.1.1) · codebase-memory-mcp @ `34efbc0` (~v0.8.x).
> See [[codegraph]] and [[codebase-memory-mcp]] for the per-project summaries.

Both are **local code knowledge graphs for AI coding agents** over MCP (tree-sitter → SQLite graph → MCP tools → fewer tool calls / tokens). The core idea is identical; the **implementation philosophy is opposite**.

**中文口訣**: 一个是「精悍务实的 TS 小刀」,一个是「论文背书的 C 重炮」。codegraph 已经赢了「现在」;cbm 押注「上限」。

## Index metrics (running colbymchenry/codegraph over each)

| Metric | codegraph (TS) | codebase-memory-mcp (C) |
|---|---|---|
| Clone size | 19 MB | **1.3 GB** (`internal/cbm` 1.2G = vendored C engine + grammars) |
| Files indexed | 283 | 1,323 (vendored grammars auto-skipped) |
| Nodes / edges | 4,589 / 13,106 | **43,422 / 111,069** |
| Index time | 1.5s | 18.5s |
| Its own DB | small | 102 MB |

(This is codegraph indexing both, so the table profiles *source scale*, not a head-to-head perf benchmark.)

## Stack & implementation

| | codegraph | codebase-memory-mcp |
|---|---|---|
| Language / dist | TypeScript, bundled Node, npm | **Pure C, single static binary, zero deps** (mac/Linux/Win) |
| Parsing | `web-tree-sitter` (**WASM**) | 158 vendored tree-sitter grammars **compiled into the binary** |
| Languages | 20+ | **158** |
| Retrieval | SQLite + **FTS5 full-text** (keyword; **no real embeddings**) | **Real vector semantic search**: bundled `nomic-embed-code` (768d int8), 11-signal scoring |
| Type resolution | tree-sitter static + specialized cross-lang bridging | **Hybrid LSP**: C reimpl of tsserver/pyright/gopls/rust-analyzer-style inference (11 langs) |
| MCP tools | **8** (explore / search / callers / callees / impact / node / files / status) | **~14** (+ Cypher `get_graph_schema`, dead-code, cross-service HTTP, cross-repo `CROSS_*`, ADR, IaC/K8s) |
| Maturity | **v1.1.1**, 19 releases | **v0.8.x**, 35 releases, pre-1.0 |

> Verified in source, not just READMEs: codegraph's `src/mcp` registers exactly 8 `codegraph_*` tools and has no embedding dependency in `package.json` (its "semantic" branding = FTS5 + structural graph). cbm vendors `vendored/nomic/` + `extract_nomic_vectors.py`, confirming real bundled embeddings.

## Killer features, each side

**codegraph — polished agent UX:**
- `codegraph_explore`: one call → relevant symbols' source + call paths (incl. dynamic-dispatch hops) + blast radius; collapses interchangeable impls to signatures.
- **Cross-language mobile bridging**: Swift↔ObjC, RN bridge/TurboModules/Fabric, native→JS events, Expo Modules — flows static parsing stops at.
- **Live-sync correctness**: per-file `⚠️` staleness banner tells the agent to Read pending files directly; connect-time `(size,mtime)`+hash reconciliation absorbs out-of-band edits.
- Benchmark (7 repos): 58% fewer tool calls, 22% faster, file reads ≈ 0.

**codebase-memory-mcp — maximalist engine + trust signaling:**
- **Speed**: Linux kernel 28M LOC / 75K files in 3 min, <1ms queries; RAM-first (LZ4 + in-memory SQLite, memory released after).
- **Real semantics + type-level accuracy** (the two rows above) — the only one of the pair with embeddings.
- **Team-shared graph artifact**: commit `.codebase-memory/graph.db.zst` (zstd 8–13:1, `merge=ours`) so teammates skip the reindex.
- **3D graph viz UI** (`localhost:9749`), Cypher queries, cross-repo intelligence, IaC indexing.
- arXiv 2603.27277 + OpenSSF Scorecard + SLSA3 + VirusTotal + "5604 tests" — heavy trust signaling.

## Star growth (GitHub API, 2026-06-25)

| | codegraph | codebase-memory-mcp |
|---|---|---|
| Stars | **54,425** | 14,469 |
| Forks | 3,340 | 1,064 |
| Created | 2026-01-18 (~5.2 mo) | 2026-02-24 (~4.0 mo) |
| **Avg growth** | **~10,400 ★/mo (~345/day)** | ~3,600 ★/mo (~120/day) |
| Releases | 19 (v1.1.1) | 35 (v0.8.1) |
| Open issues | 226 | 147 |

codegraph started ~5 weeks earlier and accrues stars ~**3×** faster — clearly winning popularity. cbm ships releases more often (35 vs 19) — rapid iteration — but hasn't dared a 1.0.

> ⚠️ **Caveat**: 54k stars in 5 months for a 2026 dev tool is *extraordinarily* viral — usually a sign of heavy promotion / list exposure. Trust the **relative trend (codegraph ≫ cbm)** over the absolute magnitudes.

## Third-party reviews & sentiment

**Independent criticism of codegraph** (agentpedia gave a rare balanced eval, not a puff piece):
- Setup confusion: "index the repo" ≠ "wire the agent" are two separate steps.
- Watch-mode resource pressure (esp. macOS); large-DB timeouts reported.
- Missed edges for language-specific patterns (Svelte/TS barrels, TS string literals).
- Explicitly: *"not a magic token reducer"*; don't stack multiple context tools; measure one real task A/B rather than trusting claimed savings. (Reviewer admitted they didn't finish a local install — perf numbers are from codegraph's own README.)

**codebase-memory-mcp**: essentially only a **4.7/5 (40 reviews)** on an MCP directory — but all from the project's own listing page; **no genuine independent third-party eval found**. Its benchmark (83% quality / 10× tokens / 2.1× calls) is from its own arXiv preprint, with reference answers **graded by the first author** — independence is questionable.

## Real issue-tracker signal (the most authentic "review")

**codegraph** (tuning / feature-request flavor — mature):
- Shared daemon serializes concurrent tool calls → N clients = N× tail latency.
- explore relevance: generic tokens overboost on exact name-match.
- A user: *"Codegraph consumes more tokens than just talking to Claude Code."*
- v1.1.0 Codex MCP "transport closed" bug; nested-repo indexing controls.

**codebase-memory-mcp** (crash / not-implemented / unusable — young + C complexity + huge surface):
- **`query_graph` crashes.**
- `hook_augment` PreToolUse augmenter is a **no-op on Windows** (POSIX-only paths).
- Advertised `ingest_traces` is **currently a no-op stub**.
- **"Completely unusable after botched upgrade."**

## A "read the source vs trust the marketing" lesson

Aggregators (claudemarketplaces, some search results) report codegraph as **"38 languages / 42 MCP tools / 94% savings / 82% faster."** Reading the **v1.1.1 source** instead: **8 MCP tools, 20+ languages, README claims 58% fewer calls / 22% faster.** The gap is version drift — v0.9.x had a more granular tool surface, consolidated into 8 around `codegraph_explore` for v1.x. **Third-party numbers lag the actual repo by a major version** — which is exactly why this repo learns by reading source.

## Verdict

| Dimension | Winner | Why |
|---|---|---|
| Popularity / momentum | **codegraph** | ~3× star growth, already 1.0, issues are tuning-grade |
| Maturity / stability | **codegraph** | cbm still in crash / no-op / botched-upgrade territory |
| Capability ceiling / ambition | **codebase-memory-mcp** | real embeddings + Hybrid LSP + Cypher + cross-repo + IaC + 158 langs + arXiv |
| Independent verification | both weak | mostly self-reported benchmarks; A/B yourself |

> **One line**: codegraph has already won *now* (popularity, stability, onboarding); codebase-memory-mcp bets on the *ceiling* (pure-C performance + semantic/type depth) but is still filling in advertised features and fixing crashes.

## Sources
- codegraph: https://github.com/colbymchenry/codegraph · https://colbymchenry.github.io/codegraph/ · agentpedia eval https://agentpedia.codes/blog/codegraph-guide · listing https://claudemarketplaces.com/mcp/codegraph-ai/codegraph
- codebase-memory-mcp: https://github.com/DeusData/codebase-memory-mcp · https://deusdata.github.io/codebase-memory-mcp/ · directory https://www.explainx.ai/mcp-servers/codebase-memory-mcp · paper https://arxiv.org/abs/2603.27277
