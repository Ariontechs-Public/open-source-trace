# Superpowers — The Harness Model

> Captured 2026-06-26 while re-tracing `v5.1.0 → v6.0.3`. Authoritative source:
> `agents/superpowers/docs/porting-to-a-new-harness.md` + `skills/using-superpowers/references/*-tools.md`.
> When this note and the upstream code disagree, the code wins.

## What "harness" means

> **A harness is the agent runtime that runs the skills — an IDE, CLI, or agent
> runner.** Claude Code is one harness; Codex, Gemini CLI, OpenCode, pi, Kimi,
> Cursor, Copilot CLI are others.

The skill content under `skills/` is **identical everywhere** (harness-agnostic).
What changes per harness is the thin layer that (a) delivers that content to the
model and (b) translates the model's instructions into the harness's native tools.

**中文口訣**: skill 是燃料,harness 是引擎,中間靠 bootstrap + tool mapping 接起來。

## The three components of an integration

| Component | Per-harness? | Role |
|---|---|---|
| **① Skills** | shared, verbatim | The real content. Describes *actions* only — "dispatch a subagent", "read a file", "create a todo" — **never** a concrete tool name. This is what lets one skill body run on every harness unedited. |
| **② Tool mapping** | per-harness | Translates the action vocabulary into the harness's real tool names. Lives in `skills/using-superpowers/references/<harness>-tools.md`. e.g. *dispatch a subagent* → Claude Code `Task(subagent_type=…)`. |
| **③ Bootstrap** | per-harness | At session start, the full `using-superpowers/SKILL.md` is injected into context wrapped in `<EXTREMELY_IMPORTANT>`, with the tool mapping appended. **"The bootstrap is the entire integration"** — without it the skill files are inert, present on disk but never invoked. |

### Two rules that make it work
1. **Skills name actions, not tools.** Never edit a skill body to fit a harness.
   Porting = add a tool-mapping reference + a bootstrap injector, nothing else.
2. **Bootstrap is everything.** The injected `using-superpowers` skill is what
   teaches the model that skills exist and that it must check for a relevant one
   before acting.

## Supported harnesses (v6 — Appendix A of the porting guide)

| Harness | Bootstrap mechanism | Mechanism class |
|---|---|---|
| **Claude Code** | shell hook → `hooks/session-start` (`additionalContext`) + native `Skill` tool | A — hook |
| **Codex** | shell hook → `session-start-codex`; fork sync via `scripts/sync-to-codex-plugin.sh` | A — hook |
| **Cursor** | shell hook → `session-start` (`additional_context`) | A — hook |
| **Copilot CLI** | shares Claude Code hook path (`COPILOT_CLI` env) | A — hook |
| **Gemini CLI** | `GEMINI.md` `@`-includes bootstrap + mapping | instructions-file |
| **Kimi Code** | manifest `sessionStart.skill` loads `using-superpowers`; inline `skillInstructions` | C — plugin installer / manifest |
| **OpenCode** | in-process plugin `.opencode/plugins/superpowers.js`: `config` hook registers skills dir, `experimental.chat.messages.transform` injects the user message | B — in-process callback |
| **pi** | in-process extension `.pi/extensions/superpowers.ts`: `resources_discover` registers skills, `context` event injects user message; lifecycle-flag + compaction-aware | B — in-process callback |

Plus **Antigravity** has a prepared `references/antigravity-tools.md` (mechanism C
via `agy plugin install` shipping a generated context file) — an in-progress target.

## The three bootstrap mechanism classes

Pick by what the harness can do:

- **A — session-start hook.** Harness fires a shell hook at session start that can
  return `additionalContext`. → Claude Code, Codex, Cursor, Copilot CLI.
- **B — in-process callback.** Harness is a JS/TS plugin host with session/message
  lifecycle callbacks you can mutate. → OpenCode, pi.
- **C — plugin installer + manifest context file.** Harness has an install command
  and a manifest `contextFileName` (or equivalent) the installer preserves. → Kimi,
  Antigravity.

## Why this is a v6 headline

A large slice of the 169 commits in `v5.1.0..v6.0.3` widened this matrix: new
Kimi / pi / Antigravity support, the `references/*-tools.md` set filled out to six
files, and new harness tests — `tests/kimi/`, `tests/pi/`,
`tests/opencode/test-bootstrap-caching.mjs`, `tests/hooks/test-session-start.sh`
(+240), `tests/shell-lint/` (+179).

## Cross-link to this repo

**OpenCode — which we also trace at `cli-tools/opencode/` — is itself a supported
superpowers harness** (mechanism B, `.opencode/plugins/superpowers.js`). The two
tracked projects meet here: opencode is simultaneously a Claude-Code-style harness
*and* a host that can run superpowers skills. See [[opencode]].

**🔑 中文口訣**: harness = 引擎,skill = 燃料;v6 把同一桶燃料調得能灌進 8 種引擎,靠的是「動作不寫死 tool」+「bootstrap 把說明書塞進每次開機」。
