# CLAUDE.md — open-source-trace

This repo is a **learning instrument**. Notable open-source projects (mostly AI / coding agents / LLM tooling) are pulled in as git submodules and studied. Notes, knowledge graphs, and structured metadata live alongside — **never inside** — the upstream code.

Mission: **靠 source code 學東西** — *learn by reading the source*.

## The Three Trees

Every tracked project is split across three concerns. Each tree has exactly one owner and one responsibility (the graph tree now nests inside the source submodule rather than living in its own top-level directory):

| Tree | Path | Owner | Mutable here? |
|---|---|---|---|
| Source | `<category>/<slug>/` | upstream (git submodule) | **No — read-only** |
| Knowledge graph | `<category>/<slug>/.codegraph/` | machine (codegraph) | Regenerate, don't hand-edit; gitignored, not committed |
| Human notes | `traces/<category>/<slug>/` | us | Yes |

> **Graph tree = codegraph (replaces the old `graphify-out/`).** The knowledge graph is now a live
> codegraph (`~/.local/bin/codegraph`) SQLite index built with `codegraph init <category>/<slug>` and queried through the
> codegraph MCP (`codegraph_explore`, `codegraph_search`, …). It lives in a `.codegraph/` directory inside
> each submodule, is **gitignored and regenerable** (never committed), and is the one tolerated exception to
> Critical Rule #1 — it is machine index state, not an edit to upstream source.

Categories at top level: `agents/`, `cli-tools/`, `frameworks/`, `apps/`. Add new categories as needed (e.g., `models/`).

## Critical Rules

1. **Submodule directories are read-only.** Never edit files inside `agents/<X>/`, `cli-tools/<X>/`, etc. Never `git add` changes from inside them. If an idea needs forking, copy into `experiments/<name>/` or `traces/<cat>/<slug>/notes/`.

2. **All human-authored content goes in `traces/<cat>/<slug>/`** — not in the submodule, not at repo root.

3. **The knowledge graph is a codegraph index at `<category>/<slug>/.codegraph/`** — build it with `codegraph init <category>/<slug>` and query it via the codegraph MCP. It is gitignored and regenerable; never commit it and never hand-edit it.

4. **`traces/<cat>/<slug>/meta.yml` is the single source of truth** for the project index and the content-pipeline. Update it whenever you re-trace.

5. **No article drafts in this repo.** Articles are published from `~/projects/ariontechs-platform/apps/content-pipeline`, which reads `meta.yml` + `summary.md` from here. (It formerly also consumed the committed `graphify-out/`; that tree is gone — the graph is now a local codegraph index.)

6. **Language convention.** English in code, file paths, identifiers, and most prose. **中文口訣** (key mantras / one-liners) are welcome in `summary.md` callouts and in `meta.yml` `one_line` — they are part of the learning style of this repo.

## Adding a New Project

> Run these commands from the repo root. Slugs SHOULD be globally unique across categories — if `cli-tools/foo/` exists, don't add `agents/foo/`.

```bash
# 1. Pick category + canonical slug (lowercase-kebab, matches upstream name)
CAT=agents          # agents | cli-tools | frameworks | apps
SLUG=superpowers

# 2. Add upstream as a submodule
git submodule add <upstream-url> $CAT/$SLUG

# 3. Scaffold the trace directory (meta.yml + summary.md are REQUIRED; notes/ is OPTIONAL)
mkdir -p traces/$CAT/$SLUG
$EDITOR traces/$CAT/$SLUG/meta.yml      # see schema below
$EDITOR traces/$CAT/$SLUG/summary.md    # 5 H2 sections, see template
# When you start writing a long-form deep dive:
#   mkdir -p traces/$CAT/$SLUG/notes && $EDITOR traces/$CAT/$SLUG/notes/<topic>.md

# 4. (Optional) Build the knowledge graph (codegraph)
codegraph init $CAT/$SLUG
# Index lands in $CAT/$SLUG/.codegraph/ — gitignored, NOT committed. Query via the codegraph MCP.

# 5. Update the README INDEX block, then commit at root
#    (.codegraph/ is gitignored, so there is nothing to `git add` for the graph.)
git add .gitmodules $CAT/$SLUG traces/$CAT/$SLUG README.md
git commit -m "trace: add $CAT/$SLUG"
```

## Re-tracing an Updated Upstream

> Moving a submodule to a new upstream ref is **not** editing the submodule — we're changing the pointer this repo holds, not modifying upstream's file contents. Editing files **inside** the submodule directory is still forbidden (Critical Rule #1).

```bash
CAT=agents
SLUG=superpowers

# 1. Move the submodule pointer to the new upstream ref (no cwd change needed)
git -C $CAT/$SLUG fetch
git -C $CAT/$SLUG checkout <new-tag-or-commit>

# 2. Bump meta.yml
$EDITOR traces/$CAT/$SLUG/meta.yml
#   upstream.pinned_commit: <new-tag-or-commit>
#   last_traced: <today>

# 3. Rebuild the codegraph index if the change is substantial
codegraph init $CAT/$SLUG   # (or `codegraph sync $CAT/$SLUG` for an incremental update)

# 4. Update summary.md (learned / open-questions) as new insight lands

# 5. Commit at root (.codegraph/ is gitignored — nothing to add for the graph)
git add $CAT/$SLUG traces/$CAT/$SLUG
git commit -m "trace: re-trace $CAT/$SLUG @ <new-version>"
```

## `meta.yml` Schema

A worked example lives at `traces/agents/superpowers/meta.yml`. Required fields:

- `name` — display name
- `category` — MUST equal the parent directory name
- `upstream.url` — git URL
- `upstream.pinned_commit` — tag/sha matching the submodule checkout
- `upstream.license` — SPDX id
- `status` — lifecycle stage:
  - `tracing` — submodule added; actively reading source and taking notes
  - `baseline` — first reading complete; `summary.md` usable but intend to deepen
  - `pass-1-done` — full first trace pass written; deep dives in `notes/`
  - `archived` — no longer actively tracked (deprecated upstream, lost interest, superseded)
- `one_line` — single-sentence TL;DR (English; a 中文 version may follow on a new line)
- `tags` — free-form array
- `added` — `YYYY-MM-DD` *(unquoted — let YAML parse as a Date, not a string)*

Optional: `last_traced`, `learned`, `related` (a list of `"[[slug]]"` strings — quote them so YAML doesn't parse `[[...]]` as a nested sequence), `articles`.

## `summary.md` Template

Five `##` H2 sections, **in this fixed order**. The content-pipeline parses by header — do not rename or reorder.

```markdown
# <Project>

## TL;DR
English paragraph.

**中文口訣**: one-liner.

## Why I Care
2-3 sentences on what this informs in my own work.

## Killer Ideas
- Design decisions worth stealing, with links into source.

## Open Questions
- What to investigate in the next trace pass.

## Map
- [Architecture deep dive](notes/architecture.md)
- Knowledge graph (codegraph, live): `<cat>/<slug>/.codegraph/` — query via the codegraph MCP.
- Upstream: <url>
```

## Where Things Live

- `<category>/` (source + its gitignored `.codegraph/` index) and `traces/` — the three trees per tracked project (see The Three Trees table above)
- `docs/superpowers/specs/` — design docs / spec for this repo's own evolution (e.g., this layout was specified in `docs/superpowers/specs/2026-05-19-open-source-trace-design.md`)
- `experiments/<name>/` — our own POCs and applications, NOT for tracking other people's projects
- `scripts/` — repo maintenance (planned: `build-index.*` to regenerate the README INDEX block)
- `.claude/settings.json` — committed Claude Code config. Currently used to exclude submodule `CLAUDE.md` files from auto-discovery via `claudeMdExcludes`, so each submodule's upstream CLAUDE.md (intended for THEIR contributors) doesn't pollute this repo's session context.

## Related Repos

- `~/projects/ariontechs-platform/apps/content-pipeline` — publishes articles derived from `meta.yml` + `summary.md` in this repo (it previously also read the committed `graphify-out/`, now replaced by a local codegraph index)

## Don't

- Don't add per-project README files under `<category>/`. Use `traces/<cat>/<slug>/summary.md`.
- Don't let `meta.yml` `category:` drift from the parent directory name — they MUST match.
- Don't reuse a slug across categories. Slugs are globally unique (`agents/foo/` and `cli-tools/foo/` cannot coexist).
- Don't commit the codegraph index (`.codegraph/`) — it is gitignored, regenerable machine state. Rebuild with `codegraph init <cat>/<slug>`, don't hand-edit it.
- Don't write article drafts in this repo.
- Don't rename or reorder the H2 sections of `summary.md` — the pipeline depends on them.
- Don't commit secrets, `.env` files, or large binary fixtures.
