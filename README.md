# open-source-trace

> **靠 source code 學東西** — learning AI/agent systems by reading their source.

A study-in-the-open repository. Notable open-source projects — mostly AI, coding agents, and LLM tooling — are pulled in as git submodules and dissected. Knowledge graphs, human notes, and structured metadata live **alongside** the upstream code, never **inside** it. Some notes are republished as articles on [ariontechs.com](https://ariontechs.com).

Public on purpose. Read along, fork your own, or just learn from the worked examples.

## The Three Trees

Every tracked project lives in three parallel places — one tree per kind of content:

```
agents/superpowers/                ← source        (git submodule, upstream)
graphify-out/agents/superpowers/   ← graph         (machine, regenerable)
traces/agents/superpowers/         ← notes         (human: meta.yml, summary.md, notes/)
```

> **口訣**：source 在原樹、圖譜鏡射在 `graphify-out/`、筆記鏡射在 `traces/`。
> 三棵樹各管一件事，不互相污染。

## Categories

| Directory | What goes here |
|---|---|
| `agents/` | Autonomous agent systems (Superpowers, gstack, …) |
| `cli-tools/` | Terminal developer tools (Claude Code, opencode, nano-claude, …) |
| `frameworks/` | Libraries / frameworks for building agents or LLM apps |
| `apps/` | Full applications / products |

New categories (e.g., `models/`) are added as the scope grows.

## Project Map

<!-- INDEX:START -->

| Project | Category | Pinned | Status | One Line |
|---|---|---|---|---|
| [Superpowers](traces/agents/superpowers/summary.md) | agents | v5.1.0 | tracing | Skill-based agent system: agents invoke discrete markdown skills as tools, routed by frontmatter descriptions. |
| [gstack](traces/agents/gstack/summary.md) | agents | `026751ea` | tracing | Garry Tan's Claude Code setup: 23 persona-bundled subagent skills (CEO, Designer, Eng Manager, Reviewer, QA, SRE...) that compose via design-doc handoff. |
| [Claude Code](traces/cli-tools/claude-code/summary.md) | cli-tools | v2.1.143 | tracing | Anthropic's official agentic coding CLI: terminal-native, codebase-aware, with hooks, slash commands, MCP servers, plugins, and IDE integrations. |
| [opencode](traces/cli-tools/opencode/summary.md) | cli-tools | v1.15.5 | tracing | Open-source agentic coding CLI in the Claude Code space — multi-provider, single-binary install across mac/Win/Linux/Arch/nix, plus desktop apps. |
| [nano-claude](traces/cli-tools/nano-claude/summary.md) | cli-tools | `21ee5c6` | tracing | Minimal Claude Code reimplementation in ~2,000 lines of TypeScript (19 files, 7 tools, Node.js + readline) — explicitly designed as a learning artifact with versioned milestones v0..v8. |

<!-- INDEX:END -->

> The table above is the catalog. It will eventually be regenerated from `traces/**/meta.yml` by `scripts/build-index.*`. Until that generator lands, the block is maintained by hand.

## Getting Started

```bash
git clone --recursive https://github.com/Ariontechs-Public/open-source-trace.git
cd open-source-trace

# Forgot --recursive?
git submodule update --init --recursive
```

**New here?** Open any `traces/<category>/<project>/summary.md` — each is a one-page intro designed to read in under five minutes.

Where to look for a given project:

```
traces/<category>/<project>/summary.md      ← start here (one page)
traces/<category>/<project>/notes/          ← deep dives (optional, present only when written)
graphify-out/<category>/<project>/          ← knowledge graph
<category>/<project>/                       ← actual upstream source
```

## Adding a Project

See [CLAUDE.md → Adding a New Project](./CLAUDE.md#adding-a-new-project) for the full workflow.

TL;DR: `git submodule add` the upstream under the right category, scaffold `traces/<cat>/<slug>/` with `meta.yml` + `summary.md`, optionally run `/graphify`, commit at root.

## Publishing

This repo is **raw material**, not a publishing platform. Articles are written and published by the companion repo `ariontechs-platform/apps/content-pipeline`, which reads `meta.yml`, `summary.md`, and `graphify-out/` from here.

> **口訣**：這裡寫筆記，那裡發文章。

## Attribution & Licensing

- Each `<category>/<project>/` subdirectory is an upstream project pulled in as a git submodule. **The upstream's license applies to that code** — not the license of this repo. Check `<category>/<project>/LICENSE` or `traces/<category>/<project>/meta.yml` → `upstream.license`.
- Notes, summaries, and knowledge graphs under `traces/` and `graphify-out/` are our own work. A repo-wide `LICENSE` file may be added later (current intent: CC-BY-4.0 for notes, MIT for scripts).
