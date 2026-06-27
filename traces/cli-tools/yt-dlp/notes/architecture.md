# yt-dlp — Architecture Deep Dive (pass 1)

Pinned at `2026.06.09`. All line numbers are against that checkout. Read with
`codegraph` over `cli-tools/yt-dlp/.codegraph/` — every claim below is anchored to a `file:line`.

The whole program is one pipeline with a universal contract at every seam:
**a unit of work is always a URL string in, an `info_dict` out.** Everything
interesting — 1,800 extractors, a format DSL, a JS interpreter, the plugin
system — hangs off keeping that contract narrow while letting the parts behind
it be arbitrarily complex.

---

## 1. The extractor registry: self-routing, no central map

Every supported site is an `InfoExtractor` subclass (`yt_dlp/extractor/common.py:105`)
that declares a `_VALID_URL` regex. There is **no routing table** mapping sites to
extractors. Instead each class answers for itself:

```python
# common.py:615,626
@classmethod
def _match_valid_url(cls, url):
    # compiles _VALID_URL → _VALID_URL_RE once, caches on the class
@classmethod
def suitable(cls, url):
    return cls._match_valid_url(url) is not None
```

Dispatch is a **linear scan, first match wins** (`YoutubeDL.py:1700`):

```python
for key, ie in ies.items():
    if not ie.suitable(url):
        continue
    ...
    return self.__extract_info(url, self.get_info_extractor(key), ...)
else:
    self.report_error(f'No suitable extractor found for URL {url}')
```

The ordering is load-bearing — `gen_extractor_classes()` documents *"The order does
matter; the first extractor matched is the one handling the URL"*
(`extractor/__init__.py:17`), and `GenericIE` is forced last as the catch-all
(`extractor/__init__.py:34-39`).

**How 1,800 regexes avoid collisions:** a more-general extractor defers to a
more-specific sibling by overriding `suitable()`. Canonical example
(`extractor/acast.py:137`):

```python
class ACastChannelIE(ACastBaseIE):
    @classmethod
    def suitable(cls, url):
        return False if ACastIE.suitable(url) else super().suitable(url)
```

The channel extractor's URL pattern is a superset of the episode extractor's, so it
explicitly excludes URLs the episode extractor would claim. Conflict resolution is
**distributed into the extractors themselves**, not centralized.

> **口訣**:每個 extractor 自報 `_VALID_URL`,框架線性掃描、第一個 `suitable` 命中者得標 ——
> 撞型時由「比較具體的那個」自己讓路。沒有中央路由表。

This is exactly skill/tool-frontmatter routing for an agent harness, a decade
earlier: each capability advertises what inputs it handles, and the host just asks
each one "are you suitable?"

### Lazy registration

Extractors aren't a hand-maintained list — they're registered through the *same
plugin spec* mechanism third-party plugins use (`extractor/__init__.py:9`):

```python
register_plugin_spec(PluginSpec(
    module_name='extractor', suffix='IE',
    destination=_extractors_context, plugin_destination=_plugin_ies_context))
```

`import_extractors()` (`extractor/__init__.py:53`) populates the global registry
lazily; a generated lazy-extractor list lets the CLI enumerate ~1,800 classes
without importing every body at startup. **Registry enumerable ≠ registry loaded.**

---

## 2. The extract → process pipeline

The flow codegraph traces as one call path (`YoutubeDL.py`):

```
extract_info (1671)  →  __extract_info (1857)  →  process_ie_result (1904)  →  process_video_result (2827)
```

- **`extract_info` (1671)** — finds the suitable extractor (§1), checks
  `ie.working()` (broken extractors emit a warning but still run, 1704), checks the
  download archive for early-exit (`break_on_existing`, 1709-1714).
- **`__extract_info` (1857)** — calls `ie.extract(url)` (the public wrapper at
  `common.py:755` around each extractor's `_real_extract`), normalizes legacy
  list results into `{'_type': 'compat_list', ...}` (1871), then hands off to
  `process_ie_result` when `process=True`.
- **`process_ie_result` (1904)** — the recursive resolver. It dispatches on the
  `_type` discriminator in the info dict: `video`, `playlist`, `multi_video`,
  `url`, `url_transparent`, `compat_list`. `url` / `url_transparent` results
  **re-enter extraction** — an extractor can return "go extract this other URL,"
  and the engine loops back through §1 with a different extractor. This recursion
  is how a playlist page fans out into per-video extraction across many extractors.
- **`process_video_result` (2827)** — terminal stage: format selection (§4) then
  download (§5).

### `smuggle_url` — side-band data through the universal contract

The contract is rigid: an extractor only ever receives a URL string. So how does
one extractor pass structured context to the next? It hides JSON in the URL
fragment (`utils/_utils.py:1718`):

```python
def smuggle_url(url, data):
    sdata = urllib.parse.urlencode({'__youtubedl_smuggle': json.dumps(data)})
    return url + '#' + sdata
def unsmuggle_url(smug_url, default=None):
    if '#__youtubedl_smuggle' not in smug_url: return smug_url, default
    ...  # parse the JSON back out
```

The universal `(url) -> info_dict` interface never widens, yet arbitrary
inter-extractor state still flows. A clean lesson in keeping a seam narrow while
smuggling complexity *through* it rather than *around* it.

---

## 3. Format selection is a real compiler

`build_format_selector` (`YoutubeDL.py:2332`) turns a string like
`bestvideo*+bestaudio/best[height<=720]` into an executable selector. It is a
genuine lex → parse → compile pipeline:

1. **Lex** — reuses Python's stdlib `tokenize` module (2649) rather than a custom
   lexer.
2. **Parse** — `_parse_format_selection` (2387) is a hand-rolled recursive-descent
   parser producing an AST of `FormatSelector` namedtuples (2343) with four node
   types: `SINGLE` (atom), `PICKFIRST` (`/`), `MERGE` (`+`), `GROUP` (`()`).
3. **Compile** — `_build_selector_function` (2534) walks the AST and emits **nested
   generator closures**. `/` returns the first branch that yields anything (2549);
   `+` takes the cartesian product and `_merge`s each pair (2559); a `SINGLE` atom
   like `bv*.2` is decoded by one regex (2584):
   `(best|worst|b|w)(video|audio|v|a)?(\*)?(?:\.(\d+))?`.

Filters (`[height<=720]`) are compiled separately by `_build_format_filter` and
applied as a `filter()` over the candidate formats *before* the selector runs (2633).

**The hack worth remembering** (2642): Python 3.12 changed its tokenizer so `7_a`
is no longer a valid token, which broke format IDs. The fix prefixes every number
with 32 random letters before tokenizing, then strips the prefix back off each
token (2646-2651) — with a `# TODO: Implement parser not reliant on
tokenize.tokenize`. Borrowing a stdlib lexer for a DSL it was never meant for buys
you a free, maintained tokenizer at the cost of fighting its edge cases.

`_default_format_spec` (2308) is itself adaptive: the default
`bestvideo*+bestaudio/best` is **probed against ffmpeg** — it instantiates
`FFmpegMergerPP` and checks `can_merge()`; if ffmpeg is missing it downgrades to a
pre-merged `best` and warns the user the result may differ (2317-2323). Capability
detection feeds the default, rather than a static default failing later.

---

## 4. Downloader selection: a decision tree, table as fallback

A format carries a `protocol` (http / m3u8_native / http_dash_segments / rtmp / …).
`get_suitable_downloader` (`downloader/__init__.py:4`) maps it to a `FileDownloader`
subclass — but the `PROTOCOL_MAP` dict (41) is only the *last resort*. The real
logic is the priority chain in `_get_suitable_downloader` (87):

1. `section_start/end` set → `FFmpegFD` (only ffmpeg can cut ranges, 92).
2. A user-configured external downloader (aria2c, etc.) that `can_download` (104).
3. Live m3u8/DASH → `FFmpegFD`; otherwise honor `hls_prefer_native` to pick
   `HlsFD` vs `FFmpegFD` (109-124).
4. Fall through to `PROTOCOL_MAP.get(protocol, default)` (126).

Merged formats have a `protocol` like `a+b`; `get_suitable_downloader` splits on
`+`, resolves each, and collapses the set (9-19). The dispatch shape mirrors §1:
the simple lookup table is the floor, with override logic layered above it.

---

## 5. JSInterpreter: emulating the adversary

`jsinterp.py` is a from-scratch JavaScript interpreter (`JSInterpreter`,
`jsinterp.py:271`). Its entire reason to exist is to **execute YouTube's obfuscated
player JS** and derive the `signature` / `n`-parameter transforms that gate video
URLs — without shipping a real JS engine. Notable pieces:

- `LocalNameSpace(collections.ChainMap)` (222) models JS lexical scopes;
  `JS_Undefined` is a distinct sentinel.
- `_js_ternary` (96) faithfully reproduces JS truthiness — `False/None/0/''/
  JS_Undefined/NaN` are falsy — so the emulated semantics match the browser's.
- JS control flow is modeled as Python exceptions: `JS_Throw` (216), plus
  `JS_Break`/`JS_Continue`.
- `Debugger.wrap_interpreter` (251) wraps `interpret_statement` to trace every
  evaluated statement when debugging.

This is the sharp end of yt-dlp's *adversarial maintenance*: YouTube ships new
obfuscation, yt-dlp's interpreter evaluates it anyway. Directly relevant to anyone
who has to run untrusted, hostile code (model- or site-supplied) inside a sandbox
they control.

---

## 6. Plugin system: hijacking Python's import machinery

Third-party extractors and post-processors install by being dropped into a
`yt_dlp_plugins` namespace — no registration call. `plugins.py` makes this work by
inserting a custom finder into `sys.meta_path` (`plugins.py:247`):

```python
sys.meta_path.insert(0, PluginFinder(f'{PACKAGE_NAME}.{plugin_spec.module_name}'))
```

- `PluginFinder(importlib.abc.MetaPathFinder)` (116) resolves
  `yt_dlp_plugins.extractor.*` by scanning user/system config dirs, `yt-dlp-plugins`
  folders, and `PYTHONPATH` (`default_plugin_paths`, 81) — **and inside `.zip`,
  `.egg`, `.whl` archives** via `dirs_in_zip` (69, 142). `PluginLoader` (61) is a
  dummy because these are namespace packages.
- Discovery is introspective and self-describing again (`get_regular_classes`, 182):
  a plugin is *any class whose name ends in the spec suffix* (`IE` / `PP`) in the
  right module — no decorator, no manifest.
- `load_plugins` **prepends** discovered classes to the core lookup
  (`merge_dicts(regular_classes, destination)`, 232), so a plugin can **override a
  built-in extractor** by claiming a higher position in the first-match scan.
- `YTDLP_NO_PLUGINS` env var is the kill switch (197).

The same `PluginSpec` abstraction powers core extractors (§1), core
post-processors, and third-party additions — one extension mechanism, used
uniformly all the way down.

---

## Cross-cutting lessons (for agent/tooling work)

- **Self-describing capabilities + host-driven `suitable()` scan** beats a central
  registry when the capability set is large and community-maintained. (§1, §6)
- **Keep the seam narrow, smuggle complexity through it.** `(url) -> info_dict`
  never changes; `smuggle_url` carries side-band data without widening the
  contract. (§2)
- **A DSL absorbs combinatorial intent** a flag matrix can't — and you can lean on
  a stdlib lexer to get one cheaply. (§3)
- **Lookup table as the floor, decision tree above it** is the recurring dispatch
  shape (§1 ordering, §4 downloader priority, §6 plugin prepend-to-override).
- **Emulate the adversary** when you can't out-run it: a sandboxed mini-interpreter
  for hostile code. (§5)

## Still open

- `process_video_result` (2827) internals — how thumbnails, subtitles,
  post-processor chains, and the actual `process_info` download are sequenced.
- The `FormatSorter` / `-S` sort-key language (`common.py:1942` `FormatSort`) — the
  *ranking* side, distinct from the *selection* DSL covered here.
- How CI validates ~1,800 extractors against live, changing sites
  (`test/test_all_urls.py` was the only covering test codegraph surfaced for
  `suitable`).
- The `networking/` stack (impersonation, `_download_*` retry/backoff machinery in
  `common.py:1164`).
