# CLAUDE.md — open-source-trace

This repo is a **learning instrument**. Notable open-source projects (mostly AI / coding agents / LLM tooling) are pulled in as git submodules and studied. Notes, knowledge graphs, and structured metadata live alongside — **never inside** — the upstream code.

Mission: **靠 source code 學東西** — *learn by reading the source*.

## The Three Trees

Every tracked project lives in three parallel directories. Each tree has exactly one owner and one responsibility:

| Tree | Path | Owner | Mutable here? |
|---|---|---|---|
| Source | `<category>/<slug>/` | upstream (git submodule) | **No — read-only** |
| Knowledge graph | `graphify-out/<category>/<slug>/` | machine (graphify) | Regenerate, don't hand-edit |
| Human notes | `traces/<category>/<slug>/` | us | Yes |

Categories at top level: `agents/`, `cli-tools/`, `frameworks/`, `apps/`. Add new categories as needed (e.g., `models/`).

## Critical Rules

1. **Submodule directories are read-only.** Never edit files inside `agents/<X>/`, `cli-tools/<X>/`, etc. Never `git add` changes from inside them. If an idea needs forking, copy into `experiments/<name>/` or `traces/<cat>/<slug>/notes/`.

2. **All human-authored content goes in `traces/<cat>/<slug>/`** — not in the submodule, not at repo root.

3. **graphify output goes in `graphify-out/<category>/<slug>/`** — mirrors the source tree. Generate via `/graphify <path>`.

4. **`traces/<cat>/<slug>/meta.yml` is the single source of truth** for the project index and the content-pipeline. Update it whenever you re-trace.

5. **No article drafts in this repo.** Articles are published from `~/projects/ariontechs-platform/apps/content-pipeline`, which reads `meta.yml` + `summary.md` + `graphify-out/` from here.

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

# 4. (Optional) Generate the knowledge graph
/graphify $CAT/$SLUG
# Output lands in graphify-out/$CAT/$SLUG/

# 5. Update the README INDEX block, then commit at root
git add .gitmodules $CAT/$SLUG traces/$CAT/$SLUG README.md
[ -d graphify-out/$CAT/$SLUG ] && git add graphify-out/$CAT/$SLUG   # only if step 4 ran
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

# 3. Re-run graphify if the change is substantial
/graphify $CAT/$SLUG

# 4. Update summary.md (learned / open-questions) as new insight lands

# 5. Commit at root
git add $CAT/$SLUG traces/$CAT/$SLUG
[ -d graphify-out/$CAT/$SLUG ] && git add graphify-out/$CAT/$SLUG
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
- Knowledge graph: `graphify-out/<cat>/<slug>/`
- Upstream: <url>
```

## Where Things Live

- `<category>/`, `graphify-out/`, `traces/` — the three trees per tracked project (see The Three Trees table above)
- `docs/superpowers/specs/` — design docs / spec for this repo's own evolution (e.g., this layout was specified in `docs/superpowers/specs/2026-05-19-open-source-trace-design.md`)
- `experiments/<name>/` — our own POCs and applications, NOT for tracking other people's projects
- `scripts/` — repo maintenance (planned: `build-index.*` to regenerate the README INDEX block)
- `.claude/settings.json` — committed Claude Code config. Currently used to exclude submodule `CLAUDE.md` files from auto-discovery via `claudeMdExcludes`, so each submodule's upstream CLAUDE.md (intended for THEIR contributors) doesn't pollute this repo's session context.

## Related Repos

- `~/projects/ariontechs-platform/apps/content-pipeline` — publishes articles derived from `meta.yml` + `summary.md` + `graphify-out/` in this repo

## Don't

- Don't add per-project README files under `<category>/`. Use `traces/<cat>/<slug>/summary.md`.
- Don't let `meta.yml` `category:` drift from the parent directory name — they MUST match.
- Don't reuse a slug across categories. Slugs are globally unique (`agents/foo/` and `cli-tools/foo/` cannot coexist).
- Don't put graphify output inside a submodule directory.
- Don't write article drafts in this repo.
- Don't rename or reorder the H2 sections of `summary.md` — the pipeline depends on them.
- Don't commit secrets, `.env` files, or large binary fixtures.
