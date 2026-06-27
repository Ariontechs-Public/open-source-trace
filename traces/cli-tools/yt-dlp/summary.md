# yt-dlp

## TL;DR
yt-dlp is the community fork of youtube-dl: a single Python CLI that downloads media from ~1,800 sites. The architecture is the lesson, not the feature set. Every supported site is an `InfoExtractor` subclass that declares its own `_VALID_URL` regex; a classmethod `suitable(url)` matches a URL to its extractor with **no central routing table** — the registry is just "every subclass that says it's suitable." A ~4,500-line `YoutubeDL` god-object orchestrates extract → select format → download → post-process. The wildest corner is `jsinterp.py`, a hand-rolled JavaScript interpreter that exists solely to execute YouTube's obfuscated player JS and solve its signature challenges without shipping a JS engine.

**中文口訣**:每個能力自報 `_VALID_URL`,框架靠 `suitable()` 自動派工 — 沒有中央路由表的 plugin registry。

## Why I Care
This repo is mostly AI/agent tooling, and yt-dlp earns its slot as the *canonical self-describing plugin registry*: ~1,800 extractors each advertise what inputs they handle, exactly the pattern a skill/tool harness uses when it routes by frontmatter `description`. Two more transferable artifacts: `jsinterp.py` is a sandboxed mini-interpreter for untrusted adversarial code (the problem of running model- or site-supplied snippets safely), and the whole project is a decade-long case study in *adversarial maintenance* — the upstream actively breaks you, the community out-iterates it. That resilience pattern matters for any plugin ecosystem.

## Killer Ideas
- **No central registry — capabilities self-route.** `InfoExtractor.suitable()` compiles each extractor's `_VALID_URL` and matches against it (`cli-tools/yt-dlp/yt_dlp/extractor/common.py:616`). Adding a site = adding a subclass; nothing else edits. Compare to skill routing by `description`.
- **Lazy extractor list** — a generated `_extractors.py` enumerates ~1,800 classes without importing each body, so startup stays fast. Registry enumerable ≠ registry loaded.
- **A from-scratch JS interpreter as an extractor weapon.** `JSInterpreter` (`yt_dlp/jsinterp.py:271`) runs YouTube's obfuscated player JS to derive signatures — defeating an adversary by emulating its sandbox rather than fighting its output.
- **Plugins via namespace-package injection.** `PluginFinder`/`PluginLoader` (`yt_dlp/plugins.py`) use `importlib` meta-path hooks to graft third-party code into a `yt_dlp_plugins` namespace — extension without a plugin config.
- **Format selection is a DSL, not flags.** `bestvideo+bestaudio/best` with filters and `/` fallbacks is parsed in `YoutubeDL.py` — a small language absorbs combinatorial user intent a flag matrix never could.
- **Public-domain (Unlicense) as survival strategy** — the legal precondition for the youtube-dl→yt-dlp fork and for yt-dlp's own forkability.

## Open Questions
- How does the `YoutubeDL` 4.5k-line orchestrator stay maintainable — or is it the known soft spot? Map its phase boundaries (extract / filter / format-select / download / post-process).
- Format-selection grammar: where is it actually parsed, and how are filters (`[height<=720]`) evaluated against the format dict?
- Downloader strategy selection (`downloader/`): how does it pick HLS vs DASH vs native vs external (ffmpeg/aria2)?
- Extractor testing at scale — how does CI validate ~1,800 extractors against sites that change without notice?

## Map
- Knowledge graph (codegraph, live): `cli-tools/yt-dlp/.codegraph/` — query via the codegraph MCP (`codegraph init cli-tools/yt-dlp` to build).
- Core orchestrator: `cli-tools/yt-dlp/yt_dlp/YoutubeDL.py`
- Extractor base class: `cli-tools/yt-dlp/yt_dlp/extractor/common.py`
- JS interpreter: `cli-tools/yt-dlp/yt_dlp/jsinterp.py`
- Plugin loader: `cli-tools/yt-dlp/yt_dlp/plugins.py`
- Upstream: https://github.com/yt-dlp/yt-dlp
