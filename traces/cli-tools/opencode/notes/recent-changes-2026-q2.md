# opencode — Major Changes, 2026 Feb → Jun

> Captured 2026-06-26 while re-tracing `v1.15.5 → v1.17.11`. Evidence: git log over
> `2026-02-01..v1.17.11` (~5,800 commits) cross-checked against the live codegraph
> index (2,852 files / 50,209 nodes). This documents the *direction*, not a changelog.

## Velocity

Commits per month: Feb 1161 · Mar 963 · Apr 1328 · May 1551 · Jun 800.
feat-scope leaders: `app` 81 · `core` 64 · `tui` 44 · **`httpapi` 28** · **`desktop` 28** · `acp` 16.
**No breaking API changes** in the window (only two CI/symlink fixes mention "breaking")
— evolution is "grow new scaffolding without smashing the old path."

## The one-line thesis

> opencode is turning **from a CLI tool into an agent platform**: a v2 runtime as
> the engine, an HTTP API as the public surface, desktop + ACP as front-ends, and
> a skill/command registry as its own extensibility ecosystem. Sessions became
> first-class, revertable, forkable objects.

**中文口訣**: 這幾個月 opencode 在做「從工具長成平台」—— v2 runtime 當引擎、httpapi 當對外接口、desktop/acp 當前端、skill/command registry 當生態。

## The five concurrent architectural rewrites

### ① "v2" runtime rewrite (56 v2 commits) — the main line
A parallel v2 stack, confirmed in codegraph at `packages/sdk/js/src/v2/gen/sdk.gen.ts`,
built on an **Effect** runtime:
- embedded v2 session runtime + tool foundation (#30632)
- persist v2 session **context epochs** (#30789); compact context (#30986);
  interrupt execution (#30850); bound tool output (#30999)
- all UI components migrated to **v2 tokens** (#33598); v2 public error schemas (#28297)

**🔑** v2 = sessions run on opencode's own runtime, context split into compactible /
interruptible epochs.

### ② Unified HTTP API (`httpapi`, 28 commits) — the new public shape
Everything bridged onto one HTTP API: event stream (#24518), tui routes (#24548),
pty routes (#24547), session message mutations (#24487), CORS middleware (#25074).
**🔑** This is the root that lets one core feed CLI + web + desktop + editor.

### ③ Desktop app gets serious (`desktop`, 28 commits) — new product surface
server moved into a **utilityProcess** (#25962); silent install + user-scope (#26253);
clipboard (#25998) / notification (#28119) permissions; **MCP client registration +
auth** (#27525); Export Logs (#26262); Linux **AppStream MetaInfo** (#27253) +
auto-hide menu bar (#27618); pinch-zoom (#28632).
**🔑** Desktop is no longer a shell — it has its own permission model and update flow.

### ④ ACP-next (Agent Client Protocol) — new integration protocol
A clean rewrite: event routing (#29327), usage service (#29249), session state
service (#29240), directory snapshot service (#29241), pure tool/content conversion
helpers (#29232/#29231).
**🔑** A testable, pure-function protocol layer for editor integration (Zed-style ACP).

### ⑤ Its own skill/command + multi-project ecosystem
- **skill registry + file agent loading** (#30617); **command registry** (#30624);
  built-in `opencode-meta` skill (#26617); `customize-opencode` on by default (#26899)
- **session capabilities explode**: snapshot/revert (#33226), **fork**, **moving
  sessions** (#30640), model switching (#31011), switching endpoints
- **multi-project / multi-repo**: project copying & tracking (#30139), managed
  repository cache (#30408), **location filesystem contract** (#30401-ish),
  flagged project references (#30414)
- **native LLM runtime**: route Anthropic API-key models through native runtime
  (#28271), public native API (#30828)
-底層往 **Effect** (`effect-zod`, 7 commits) 函數式 runtime 收斂

## Direction table

| Axis | From | To |
|---|---|---|
| Shape | single CLI | CLI + HTTP API + Desktop + ACP front-ends over a shared v2 runtime |
| Session | linear conversation | snapshot / revert / fork / move / switch-model |
| Extensibility | built-in agents | skill registry + command registry (its own superpowers-ification) |
| Scope | single repo | multi-project / repo cache / location-FS abstraction |
| Plumbing | direct SDK calls | Effect runtime + native LLM API |

## Trace implication

The old `meta.yml` framing ("multi-provider CLI") now **undersells** it — opencode
is becoming an agent platform with an HTTP API and a desktop app, and is growing
its own skill system. Note the convergence with [[superpowers]]: opencode is both a
Claude-Code-style harness **and** a host that runs superpowers skills (mechanism B,
`.opencode/plugins/superpowers.js`).
