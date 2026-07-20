# Orca

## TL;DR
Orca is an Electron-based "Agent Development Environment" (ADE): a desktop app that runs many coding agents — Claude Code, Codex, OpenCode, Gemini, Cursor, Devin, and 20+ others — side by side, each in its own isolated git worktree, with a mobile companion app for remote monitoring. The pitch is "fan one prompt across five agents, compare, merge the winner." Under the hood it's a serious, mature codebase: a per-provider main-process module for every agent, a separate socket-based relay process that mediates PTY/git/filesystem so the same protocol works locally and over SSH, and its own internal skill-based orchestration layer for letting agents running inside Orca coordinate with each other.

**中文口訣**: 一個 prompt、多個 agent、各自 worktree、贏家合併——編排的單位是 git worktree,不是 container。

## Why I Care
This is the same "fan a prompt across N agents, judge/merge the best" pattern I use inside a single Claude Code session via subagents and the Workflow tool's `parallel()` — Orca does it at the desktop-app layer with real git worktrees and real terminal processes instead of in-session subagents. Worth studying as a production reference for what full agent-orchestration tooling looks like once it graduates past a single session: process isolation, cross-host (SSH/WSL) protocol design, and a coordination layer (task DAGs, blocking ask/reply, escalation) for agent-to-agent handoff.

## Killer Ideas
- **Worktree as the isolation primitive** — no containers, no VMs; each agent gets a native git worktree, so diffs/branches/merges are free and there's no translation layer between "what the agent did" and "what git sees."
- **Relay process as a protocol boundary** (`src/relay/*-handler.ts`) — PTY, git, and filesystem operations go through one socket protocol regardless of whether the worktree is local, WSL, or SSH-remote. Backend-agnostic execution comes from designing the *transport* once, not per-backend glue.
- **Skill routing turned into agent-to-agent orchestration** — `skills/orchestration/SKILL.md` reuses the frontmatter-`description`-routes-to-skill pattern from [[superpowers]], but applies it to coordinating *multiple* agents (task DAGs, `worker_done`/escalation waits, decision gates) rather than a single agent's own behavior.
- **AGENTS.md as enforced contributor policy, not prose** — a real Git-version/host compatibility matrix (`GitCapabilityCache`), a "why not how" comment-length rule with a concrete before/after, and a hard ban on `helpers`/`utils`/`misc` file names, all backed by `pnpm lint` checks (`check-reliability-gates.mjs`, `check-max-lines-ratchet.mjs`) that explicitly cannot be disabled.
- **Design Mode** — click a rendered UI element in the embedded browser and its HTML/CSS/screenshot go directly into the agent's prompt, short-circuiting the human-describes-the-bug step.
- **Per-feature design docs instead of one architecture doc** — ~100+ files under `docs/` (e.g. `android-emulation.md`, `cmd-j-tab-session-search.md`), each a lightweight ADR for the feature it sits next to, committed alongside the implementing code.

## Open Questions
- How does the coordinator loop / decision-gate mechanism in `skills/orchestration/SKILL.md` actually resolve conflicting or stalled agents — is there a timeout/escalation-to-human path, or does it wait indefinitely?
- What's the merge story once multiple agents finish in parallel worktrees — is "pick the winner" a manual diff-review UX, or is there automated scoring/comparison?
- How much of the 20+ provider integrations (`src/main/{claude,codex,gemini,grok,...}`) is a thin CLI-wrapper vs. deep protocol integration (e.g. streaming, tool-call interception) per agent?
- Given `pnpm-lock.yaml` at 477K and dozens of huge test files (`persistence.test.ts` at 328K, `pty-handler.test.ts` at 87K), what does the actual CI wall-clock and flakiness profile look like for a codebase this size?

## Map
- [Why / What / How — philosophy from docs, source, and git history](notes/philosophy.md)（[中文版](notes/philosophy.zh.md)）
- Knowledge graph (codegraph, live): `agents/orca/.codegraph/` — query via the codegraph MCP.
- Upstream: https://github.com/stablyai/orca
