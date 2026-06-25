# codebase-memory-mcp

## TL;DR
A maximalist code-intelligence MCP engine by DeusData, written in **pure C** and shipped as a single static binary (mac/Linux/Windows, zero deps). It vendors **158 tree-sitter grammars** into the binary, adds **Hybrid LSP** type resolution (a C reimplementation of tsserver/pyright/gopls/rust-analyzer-style algorithms for 11 languages), and — uniquely in this pair — bundles **real `nomic-embed-code` embeddings** for vector `semantic_query`. ~14 MCP tools span search, trace, architecture, impact, **Cypher** graph queries, dead-code, cross-service HTTP linking, **cross-repo `CROSS_*` edges**, ADR management, and IaC (Docker/K8s/Kustomize) indexing. RAM-first pipeline (LZ4 + in-memory SQLite) indexes the Linux kernel (28M LOC) in 3 min, queries in <1ms. arXiv-backed (2603.27277), heavy security/trust signaling (OpenSSF, SLSA3, VirusTotal), ~14k stars — but still **pre-1.0 (v0.8.x)** with stub features and crash-class issues.

**中文口訣**: 论文背书的 C 重炮 — 真 embedding + Hybrid LSP 类型推断 + Cypher + 跨 repo + IaC,全塞进单二进制;赌「越全越准越快」,但还在填坑。

## Why I Care
The opposite design bet from [[codegraph]]: where codegraph stays lean and FTS-only, this maxes out the engine — vector semantics, type-level resolution, query language, cross-repo, IaC — at the cost of a 1.3G repo, a C toolchain, and a huge surface still being filled in. It's the clearest example I've seen of "ambition vs maturity": the feature list is years ahead, but the issue tracker shows `query_graph` crashes, a Windows no-op augmenter, an advertised `ingest_traces` that's a no-op, and "unusable after a botched upgrade." Great study material for how much engineering a "code memory" actually takes when you refuse to cut scope.

## Killer Ideas
- **Embeddings compiled into the binary**: `nomic-embed-code` (768d int8, ~40K-token context) vendored in, with 11-signal combined scoring (TF-IDF, RRI, signature/AST/data-flow signals, MinHash, graph diffusion). Real semantic search with no API key, no Ollama, no Docker — the thing codegraph deliberately skips.
- **Hybrid LSP**: lightweight C implementations of language-server type resolution (parameter binding, return-type inference, generic substitution, JSX dispatch, PHP late-static-binding, C# LINQ, Kotlin scope functions, Rust UFCS) — structural accuracy beyond raw AST, without running real language servers.
- **Team-shared graph artifact**: commit `.codebase-memory/graph.db.zst` (zstd 8–13:1, indexes stripped + `VACUUM INTO`, `merge=ours` to avoid conflicts) next to source so teammates bootstrap from it and skip the full reindex. A distribution idea codegraph has no equivalent for.
- **RAM-first indexing pipeline**: LZ4 compression + in-memory SQLite + single dump at end, memory released after — how it gets the Linux kernel done in 3 minutes.
- **Breadth as a feature**: Cypher queries, cross-repo `CROSS_*` edges over a fleet, and IaC nodes (K8s `Resource`, Kustomize `Module` with `IMPORTS` edges) treat infra + multi-service as first-class graph citizens.

## Open Questions
- How real is the breadth vs how much is stub? `ingest_traces` is a confirmed no-op; what else on the 14-tool surface is aspirational?
- The benchmark (83% quality / 10× tokens / 2.1× calls across 31 repos) is from the project's own arXiv preprint, **graded by the first author** against self-derived reference answers — how does it hold up under independent A/B?
- What does the pure-C choice actually buy over codegraph's WASM tree-sitter once you account for the crash-class bugs and the Windows POSIX-only gaps?
- How does `semantic_query` (vector) compare in practice to codegraph's FTS5 on real "where/what-calls" questions — is the embedding worth the binary weight?
- Stability trajectory: how fast are the crash / botched-upgrade issues being closed, and what gates a 1.0?

## Map
- [Head-to-head vs codegraph](../codegraph/notes/vs-codebase-memory-mcp.md) — full comparison: stack, features, MCP tools, benchmarks, third-party reviews, star growth.
- Knowledge graph (codegraph, live): built 2026-06-25 from `34efbc0` — **1,323 files / 43,422 nodes / 111,069 edges / 18.5s** (vendored grammars skipped). (Indexed in a scratchpad clone; submodule not yet added — and the upstream is 1.3G, so adding it is a deliberate decision. Rebuild with `codegraph init cli-tools/codebase-memory-mcp` if/when the submodule lands.)
- Upstream: https://github.com/DeusData/codebase-memory-mcp
- Docs: https://deusdata.github.io/codebase-memory-mcp/ · Paper: https://arxiv.org/abs/2603.27277
- Compare with: [[codegraph]] (the lean TS foil)
