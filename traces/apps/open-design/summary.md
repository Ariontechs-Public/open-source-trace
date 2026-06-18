# Open Design

## TL;DR
Open Design is a **local-first, open-source "Claude Design" / Figma alternative** — a native desktop app (macOS/Windows) that reframes design as a *filesystem the agent operates on* rather than a canvas you push pixels around. Its substance is three directories: **`skills/` (157)**, **`design-systems/` (152 brand-grade `DESIGN.md` systems)**, and **`plugins/` (261)** — which any coding agent already on your laptop (Claude Code, Codex, Cursor, OpenCode, Qwen, Copilot, +22 CLIs, or any OpenAI-compatible endpoint via BYOK) can read, write, and remix to generate web/desktop/mobile prototypes, decks, images, video, and "HyperFrames" motion graphics, exported to HTML/PDF/PPTX/MP4. The codebase is a large TS monorepo (`apps/` + `packages/`) with a clear host / sidecar / plugin-runtime split, plus a hosted model service (**AMR**, the Agentic Model Router) as the commercial layer.

**中文口訣**: 設計即檔案系統 — skills + DESIGN.md + plugins,本地 agent 當設計引擎,你的 CLI 就是工作室。

## Why I Care
This is the most ambitious "agent-native app" in the trace set and the clearest articulation of a thesis I keep circling: **make the artifact a filesystem of skills + systems + plugins, and let whatever agent the user already runs drive it.** The host/sidecar/plugin-runtime decomposition is a real reference architecture for shipping an agent-native desktop product, and the `DESIGN.md`-as-brand-contract idea (shared with [[impeccable]]) is a portable primitive. The AMR layer is also a concrete example of monetizing an open-source agent app via a model-router service.

## Killer Ideas
- **Design-as-filesystem.** The product's value is `skills/` + `design-systems/` + `plugins/` on disk, not a proprietary canvas. Agent-agnostic, git-friendly, remixable. This is the core bet.
- **`DESIGN.md` as portable brand contract.** 152 brand-grade systems shipped as `DESIGN.md` files — the same primitive [[impeccable]] uses for its single language. Convergent design across two independent projects is a strong signal.
- **Host / sidecar / plugin-runtime split.** `packages/host`, `packages/sidecar` (+ `sidecar-proto`), `packages/plugin-runtime`, `packages/registry-protocol` — a clean process/protocol boundary for running untrusted plugins and swappable agent backends. Worth studying as an agent-app reference architecture.
- **Radical harness portability.** One app, ~30 agent backends, BYOK to any OpenAI-compatible endpoint. Decouples the product from any single model vendor.
- **AMR commercial layer.** An "Agentic Model Router" fronting 20+ models, billed by real token usage — open-source core + hosted convenience service as the business model.

## Open Questions
- **Skill/plugin/design-system schemas** — how are the 157 skills, 261 plugins, and 152 design systems structured and discovered? Is there a manifest/registry format (`registry-protocol`)? How does it overlap with the superpowers skill model?
- **Sidecar protocol** — what does `sidecar-proto` / `launcher-proto` actually carry? This is likely where the agent-backend abstraction lives.
- **Plugin sandboxing** — README claims "sandboxed iframe preview"; how does `plugin-runtime` isolate untrusted plugins, and what's the trust boundary?
- **How are the ~30 agent CLIs adapted?** Is there a per-harness adapter layer (like superpowers/impeccable), and where (`agui-adapter`?)?
- **Open-core boundary** — what's Apache-2.0 vs gated behind AMR / the desktop binary? Where does the open repo stop?

## Map
- Knowledge graph (codegraph, live): `apps/open-design/.codegraph/` — 2,281 files / 38,772 nodes / 102,953 edges. Query via the codegraph MCP (`codegraph_explore`, `codegraph_search`); too large to read by hand.
- Monorepo layout: `apps/open-design/apps/` (desktop, web, daemon, landing-page) + `apps/open-design/packages/` (host, platform, plugin-runtime, sidecar, registry-protocol, agui-adapter)
- Content corpus: `apps/open-design/skills/` · `design-systems/` · `plugins/`
- Upstream: <https://github.com/nexu-io/open-design> · pinned at `open-design-v0.11.0-32-gecb1f3ac0`
