# Orca — Why / What / How

Method: triangulate three sources that answer different questions. Docs (onorca.dev/docs) give the *stated* why — vendor framing, treat as a claim. Source (`AGENTS.md`, `docs/*.md`, module layout) gives the *how* — what actually got built. Git log / release notes over time give *what actually mattered* — where months of commits went, independent of what the docs choose to foreground.

## Why

Docs state the target user explicitly:

> "Orca is designed for people who already write code for a living and want to use AI as leverage — not as a replacement. It assumes you read diffs, care about commits, and keep a worktree tidy."

This is a deliberate stance against "vibe coding" tools aimed at non-engineers — Orca positions itself for people who already review diffs and own the git workflow. The underlying bet: developers already pay for multiple agent CLI subscriptions (Claude Code, Codex, Cursor, ...); Orca's job is to orchestrate what the user already owns, not compete with the agents themselves as another model/product.

Docs' own worktree-model page is notably thin on philosophical justification beyond one line: *"This is what makes parallel agents safe — they never step on each other's files."* The "why worktrees" argument is safety-through-isolation, stated plainly, without comparing to containers/branches/alternatives — suggesting the choice was pragmatic (git-native, cheap, familiar) rather than agonized over.

## What

The atomic unit, per the docs' own "Orca Model" section: **one worktree = one agent terminal = one browser tab.** Everything else is a layer on top of that unit:

- **Racing agents** — the flagship use case ("three agents trying the same bug in parallel"), a hedge against per-agent output variance rather than trusting any single agent's first attempt.
- **Review-first surface** — heavy investment in diff viewer, "Annotate AI Diff," attribution, commit/push, hosted PR/issue/Actions review. The philosophy assumes a human always reviews before shipping; the product optimizes that review step, not the generation step.
- **Cross-environment reach** — SSH worktrees, remote Orca servers, WSL support. "Leverage" only works if agents can run wherever the developer's actual repo and infra live, not a sandboxed toy checkout.
- **Automation once trust is earned** — CLI, orchestration (task DAGs, decision gates), scheduled automations, computer use, skills registry/MCP. This tier is for users who've moved past supervising individual agents and want to compose fleet-level workflows.
- **Mobile companion** — supervise, don't babysit. The fleet keeps working while the developer is away; the phone is for notifications and checking in, not driving.

## How

- **Git worktree as isolation primitive** (not containers/VMs) — cheap to create/destroy, git-native so diffs/branches/merges need no translation layer. Trust boundary is the filesystem via a dedicated checkout, not a sandboxed runtime.
- **Relay process as protocol boundary** (`src/relay/*-handler.ts`) — PTY, git, filesystem operations go through one socket protocol whether the worktree is local, WSL, or SSH-remote. This is the concrete mechanism behind "leverage anywhere."
- **Per-provider modules** (`src/main/{claude,codex,gemini,grok,cursor,devin,...}`) — adding a new agent is additive (new peer directory), not a rearchitecture. Structurally commits to staying agent-agnostic rather than betting on one agent winning.
- **Internal skill/orchestration system** (`skills/orchestration`, `skills/orca-cli`) — moves the product from "N independent agents" to "a fleet that can hand off work to itself," letting users compose larger workflows once single-agent trust is established. Reuses the frontmatter-description routing pattern seen in [[superpowers]], but applied to agent-to-agent coordination instead of a single agent's own skill selection.
- **Contributor discipline as code, not prose** (`AGENTS.md` + lint gates) — a real Git-version/host compatibility matrix, a banned-vague-filenames rule, a hard "no max-lines disable" lint gate. This is *how* a 6,887-commit, ~4-month-old codebase shipping near-daily stays legible.

## Evidence from git/release history (what actually got worked on)

Repo genesis: first commit 2026-03-16, 6,887 commits to the pinned `v1.4.147` tag — averaging roughly 50+ commits/day, consistent with the "849 releases" / near-daily-ships claim in the initial pass.

Sampling minor-version release notes shows the product's center of gravity moving over time — a signal the docs don't state directly:

- **v1.1.0** — single-agent UX polish: "unified tabs system," "polish markdown preview and rich editor." The product is still mostly about making one agent's workspace good.
- **v1.2.0** — reliability hardening: split-group terminal lifecycle fixes, SSH connection hardening, "plug memory leaks, unbounded caches, and disk accumulation." The "make one agent's session solid under real use" phase.
- **v1.4.0** — fleet legibility: "Show running agents in Activity threads," "Make Activity default-on," "Improve Orca Mobile discoverability." The hard problem shifted from *spawning* agents to *knowing what your fleet is doing* without staring at every pane — the natural next problem once parallel agents became routine rather than a novelty.

This progression (single-agent polish → reliability → fleet observability) is the philosophy revealing itself through commit history rather than through marketing copy — it tracks what breaks and what becomes confusing as the product's own "worktree = agent = tab" unit gets multiplied.

## Open method note

Release-note sampling here was a handful of minor-version tags (v1.0.12, v1.1.0, v1.2.0, v1.4.0 — v1.3.0 has no GitHub release entry, likely skipped or yanked). A fuller pass would sample more minor versions and also diff `docs/model/worktrees.md`-equivalent pages over time (if the docs site has history) to see whether the stated philosophy itself shifted, not just the feature set.
