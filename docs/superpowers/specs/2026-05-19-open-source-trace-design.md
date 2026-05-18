# open-source-trace — Repo Design Spec

- **Date:** 2026-05-19
- **Status:** Approved (sections 1–2 explicitly by user; sections 3–5 user-instructed to "全部 Pass" — proceed without per-section approval)
- **Process:** brainstormed via `superpowers:brainstorming`; per user override the next step is `superpowers:requesting-code-review`, not `writing-plans`.

## Mission

Trace, dissect, and learn from notable open-source projects — primarily AI / agent related (Superpowers, gstack, Claude Code, opencode, nano-claude, future LLM work, etc.). The repo is a **learning instrument**: the source code is the textbook.

Knowledge captured here is later republished as articles via the separate `ariontechs-platform/apps/content-pipeline` repo. No article drafts live in this repo.

Public on purpose — study-in-the-open.

> **口訣**：靠 source code 學東西。

## Top-level Layout

```
open-source-trace/
├── README.md                      English-primary, Chinese 口訣 inline
├── CLAUDE.md                      AI / contributor operational rules
├── .gitignore
├── .gitmodules                    auto-managed by `git submodule add`
│
├── agents/                        autonomous agent systems
│   └── <slug>/                    submodule → upstream
├── cli-tools/                     terminal developer tools
├── frameworks/                    libraries for building agents / LLM apps
├── apps/                          full applications / products
│
├── graphify-out/                  knowledge graphs, mirrors source tree
│   └── <category>/<slug>/
│
├── traces/                        human notes, mirrors source tree
│   └── <category>/<slug>/
│       ├── meta.yml               REQUIRED — single source of truth
│       ├── summary.md             REQUIRED — one-page TL;DR
│       └── notes/                 OPTIONAL — long-form deep dives
│
├── experiments/                   our own POCs / applications
│   └── <name>/
│
├── scripts/                       repo maintenance (index generator — deferred)
│
└── docs/superpowers/specs/        design docs (this file lives here)
```

### The Three Trees (the core idea)

Every tracked project exists in three parallel directories, each with exactly one owner:

| Tree | Path | Owner | Mutable by us? |
|---|---|---|---|
| Source | `<category>/<slug>/` | upstream (git submodule) | **No** — read-only |
| Knowledge graph | `graphify-out/<category>/<slug>/` | machine (graphify) | Regenerate, don't hand-edit |
| Human notes | `traces/<category>/<slug>/` | us | Yes |

Three trees, one responsibility each, never cross-contaminate. Adding a new auxiliary tree (`benchmarks/`, `diffs/`, …) in the future follows the same mirror-the-source-tree rule.

### Why this shape

- **Submodule + sibling notes**: we never modify upstream code, but we want our learning to live next to (not inside) it. The mirror tree solves this without polluting the submodule.
- **`graphify-out/` and `traces/` at root**: they belong to *us* and are versioned by *our* repo's git history. Submodule history stays clean.
- **Categories at top level** (`agents/`, `cli-tools/`, …): turns the directory listing into the primary taxonomy. New categories added as needed (e.g., `models/`).

### Versioning policy

- `traces/` — committed (it's our writing, the whole point of the repo).
- `graphify-out/` — **committed**, despite being machine-generated. Rationale: this is a study-in-the-open repo; a visitor cloning it should be able to read the knowledge graphs without first installing graphify locally. Treat the committed graphs as "regenerated, not hand-edited" — re-run graphify and commit the new output rather than editing files in `graphify-out/` directly.
- Submodules — pinned by the submodule pointer (a single sha in the repo's git tree, not the upstream's file contents).

### Slug uniqueness

Slugs (the directory name under a category, e.g., `superpowers` in `agents/superpowers/`) are **globally unique across categories**. If `cli-tools/foo/` exists, do not add `agents/foo/`. The future `scripts/build-index.*` will enforce this; until then, check by hand when adding a project.

## Categories (initial)

- `agents/` — autonomous agent systems (Superpowers, gstack, …)
- `cli-tools/` — terminal developer tools (Claude Code, opencode, nano-claude, …)
- `frameworks/` — libraries / frameworks for building agents or LLM apps
- `apps/` — full applications / products

## Catalog Protocol — `traces/<cat>/<slug>/`

Each tracked project gets exactly this layout:

```
traces/<cat>/<slug>/
├── meta.yml           REQUIRED
├── summary.md         REQUIRED
└── notes/             OPTIONAL
```

### `meta.yml` schema

```yaml
# Required
name: <Display Name>
category: <agents | cli-tools | frameworks | apps>
upstream:
  url: <git URL>
  pinned_commit: <tag, sha, or version corresponding to the submodule checkout>
  license: <SPDX id, e.g. MIT>
status: <tracing | baseline | pass-1-done | archived>
one_line: "<one-sentence TL;DR>"
tags: [<free-form>]
added: <YYYY-MM-DD>

# Optional
last_traced: <YYYY-MM-DD>
learned:
  - "<insight worth keeping>"
related:                           # double-bracket links to other traces, as strings
  - "[[other-slug]]"
articles:
  - title: "<article title>"
    url: "<published URL>"
    published: <YYYY-MM-DD>
```

Rules:
- `pinned_commit` mirrors what the submodule currently points to. Bump it when re-tracing a newer upstream version.
- `category` value MUST equal the parent directory name.
- `slug` (directory name) is lowercase-kebab, SHOULD match the upstream repo name, and MUST be globally unique across categories (see Slug uniqueness above).
- `status` values:
  - `tracing` — submodule added; actively reading source and taking notes
  - `baseline` — first reading complete; `summary.md` usable but intend to deepen
  - `pass-1-done` — full first trace pass written; deep dives in `notes/`
  - `archived` — no longer actively tracked
- Date fields (`added`, `last_traced`) — unquoted ISO `YYYY-MM-DD` so YAML parses them as Date, not String.
- `related` — list of `"[[slug]]"` strings; quote them, else YAML parses `[[...]]` as a nested sequence.

### `summary.md` structure

Five `##` H2 sections, **in this fixed order**. The content-pipeline parses by header, so the order is contractual:

1. **TL;DR** — one English paragraph + one `**中文口訣**:` one-liner
2. **Why I Care** — 2–3 sentences on what problem in my own work this informs
3. **Killer Ideas** — bullets, the design decisions worth stealing, with links into source
4. **Open Questions** — bullets, what to investigate in the next trace pass
5. **Map** — pointers into `notes/`, `graphify-out/`, upstream

Long-form analysis lives in `notes/*.md` and is linked from the Map section.

## README

English primary, 中文口訣 in blockquote callouts. Sections (in order):

1. Title + tagline
2. Mission / what-this-is paragraph
3. The Three Trees (with diagram)
4. Categories (one-liners)
5. **Project Map** — table between `<!-- INDEX:START -->` / `<!-- INDEX:END -->` markers. Hand-maintained until a generator script exists.
6. Getting Started (clone with `--recursive`, where to look first)
7. Adding a Project (one-paragraph TL;DR, link to CLAUDE.md for the full workflow)
8. Publishing (pointer to content-pipeline)
9. Attribution & Licensing (upstream licenses apply to submodules)

## CLAUDE.md

English. Operational rules for AI / contributors working in this repo.

Contents:
- Mission paragraph
- The Three Trees table
- **Critical Rules** (numbered):
  1. Submodule directories are read-only — never edit, never commit changes from inside
  2. All human-authored content goes in `traces/<cat>/<slug>/`
  3. graphify output goes in `graphify-out/<cat>/<slug>/`
  4. `traces/<cat>/<slug>/meta.yml` is the single source of truth for the index and the pipeline
  5. No article drafts here — articles live in `ariontechs-platform/apps/content-pipeline`
  6. Language: English in code/identifiers/most prose; 中文口訣 welcome in `summary.md` callouts and `meta.yml` `one_line`
- Workflows:
  - Adding a new project (5-step shell-block recipe)
  - Re-tracing an updated upstream (3-step recipe)
- `meta.yml` schema (concise, points to the worked example)
- `summary.md` template (the 5 H2 sections)
- Where things live (`docs/`, `experiments/`, `scripts/`)
- Related repos (`content-pipeline`)
- Don't (anti-patterns)

## Index Generator (deferred)

`scripts/build-index.<lang>` will scan `traces/**/meta.yml` and rewrite the INDEX block in `README.md`. **Deferred** until 3+ projects exist; until then the table is hand-maintained. Language/runtime to be decided when implementation begins (likely Node + `js-yaml` to stay consistent with the `ariontechs-platform` toolchain).

## Out of Scope (deliberately)

- **Article drafting** — delegated to `content-pipeline`
- **LICENSE file** — user to choose later (CC-BY-4.0 for notes + MIT for scripts is the current intent; submodules retain their upstream licenses regardless)
- **`experiments/` scaffolding** — created ad hoc per experiment
- **CI / linting** — added when needed
- **`build-index` implementation** — deferred until catalog has >3 entries

## Decisions Log

| # | Decision | Reasoning |
|---|---|---|
| D1 | Submodules over vendoring or "no source" | User answered Q1: tracks upstream, repo stays small, clean licensing |
| D2 | Bilingual: English primary + 中文口訣 inline | User answered Q2: public-facing intent + the user's own writing rhythm |
| D3 | Category-first taxonomy (`agents/`, `cli-tools/`, …) | User answered Q3: simple, horizontally comparable, supports future `models/` |
| D4 | `traces/` + `graphify-out/` as mirror trees at root, not inside submodules | User clarified Q4: never pollute upstream; our notes evolve in our own git history |
| D5 | Articles NOT in this repo | User clarified Q5: handled by separate `content-pipeline` repo |
| D6 | Hybrid Catalog (Option C) over minimal/structured extremes | User chose C: convention-driven, machine-readable, low maintenance |
| D7 | `traces/` (no leading dot) as the mirror tree name | Visibility in Finder; concept already established by `graphify-out/` |
| D8 | `summary.md` has fixed 5-H2 contract | Required for content-pipeline to parse reliably |
| D9 | Skip `writing-plans` skill; jump to `requesting-code-review` | User explicit override |
| D10 | `graphify-out/` is committed (not gitignored) | Study-in-the-open: visitors should read graphs without needing graphify locally |
| D11 | Slugs are globally unique across categories | Avoids ambiguity in `build-index` and cross-references; cheap to enforce |
| D12 | `notes/` directory exists only when there are notes | Spec says optional; example was reverted to not pre-create empty `notes/` |

## Follow-ups (out of scope this pass)

- `scripts/build-index.*` — generator that scans `traces/**/meta.yml`, rewrites README INDEX block, and validates `category == parent dir`, slug uniqueness, required fields.
- `scripts/check-meta.*` — lightweight precursor (bash + grep, ~10 lines) that just runs the category-drift and slug-uniqueness checks before the full generator lands.
- `LICENSE` — user to choose. Suggested: CC-BY-4.0 for notes, MIT for scripts; submodules retain upstream licenses regardless.
- `meta.yml` `schema_version:` field — defer; add when the first schema-breaking change is needed.
