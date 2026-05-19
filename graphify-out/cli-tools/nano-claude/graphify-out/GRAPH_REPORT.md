# Graph Report - /Users/natchung/projects/open-source-trace/cli-tools/nano-claude  (2026-05-19)

## Corpus Check
- Corpus is ~11,013 words - fits in a single context window. You may not need a graph.

## Summary
- 195 nodes · 315 edges · 18 communities (15 shown, 3 thin omitted)
- Extraction: 91% EXTRACTED · 9% INFERRED · 0% AMBIGUOUS · INFERRED: 27 edges (avg confidence: 0.85)
- Token cost: 86,088 input · 36,895 output

## Community Hubs (Navigation)
- [[_COMMUNITY_API & Main Loop|API & Main Loop]]
- [[_COMMUNITY_Core Functions (cross-module)|Core Functions (cross-module)]]
- [[_COMMUNITY_Architecture Concepts|Architecture Concepts]]
- [[_COMMUNITY_Tool Class Hierarchy|Tool Class Hierarchy]]
- [[_COMMUNITY_System Prompt & Context|System Prompt & Context]]
- [[_COMMUNITY_Context Window Management|Context Window Management]]
- [[_COMMUNITY_Tool Implementations|Tool Implementations]]
- [[_COMMUNITY_CLAUDE.md & Memory|CLAUDE.md & Memory]]
- [[_COMMUNITY_Permission System|Permission System]]
- [[_COMMUNITY_Permission Design Rationale|Permission Design Rationale]]
- [[_COMMUNITY_Session Persistence|Session Persistence]]
- [[_COMMUNITY_Project Logo (pixel-art)|Project Logo (pixel-art)]]
- [[_COMMUNITY_README & Learning Guide|README & Learning Guide]]
- [[_COMMUNITY_Markdown Rendering|Markdown Rendering]]
- [[_COMMUNITY_CLI Args & Print Mode|CLI Args & Print Mode]]
- [[_COMMUNITY_srcprompt.ts (file)|src/prompt.ts (file)]]
- [[_COMMUNITY_srctypes.ts (file)|src/types.ts (file)]]
- [[_COMMUNITY_newSessionId helper|newSessionId helper]]

## God Nodes (most connected - your core abstractions)
1. `main` - 11 edges
2. `Tool` - 10 edges
3. `runConversationLoop` - 10 edges
4. `main()` - 9 edges
5. `runConversationLoop()` - 9 edges
6. `handleCommand()` - 9 edges
7. `handleCommand` - 9 edges
8. `src/tools/index.ts (Tool registry)` - 8 edges
9. `allTools` - 8 edges
10. `buildSystemPrompt()` - 7 edges

## Surprising Connections (you probably didn't know these)
- `main()` --calls--> `buildSystemPrompt()`  [EXTRACTED]
  index.ts → prompt.ts
- `runConversationLoop()` --calls--> `classifyToolRisk()`  [EXTRACTED]
  index.ts → permissions.ts
- `runConversationLoop()` --calls--> `askPermission()`  [EXTRACTED]
  index.ts → permissions.ts
- `handleCommand()` --calls--> `loadSession()`  [EXTRACTED]
  index.ts → session.ts
- `handleCommand()` --calls--> `printSessionList()`  [EXTRACTED]
  index.ts → session.ts

## Hyperedges (group relationships)
- **Agentic loop: API streaming + tool execution + alternating message roles** — concept_tooluseloop, concept_streamingapi, concept_toolresultasuser, concept_messagesarray [EXTRACTED 1.00]
- **Context-window management strategies** — concept_contextwindowmgmt, concept_smartcompactalgo, concept_multilevelclaudemd, concept_persistentmemory [EXTRACTED 1.00]
- **System prompt assembly sources** — fn_buildsystemprompt, concept_multilevelclaudemd, concept_persistentmemory, concept_systemprompt_assembly [EXTRACTED 1.00]
- **All Tool implementations** — bash_bashtool, read_readtool, write_writetool, edit_edittool, glob_globtool, grep_greptool, agent_agenttool [EXTRACTED 1.00]
- **System prompt input sources** — prompt_buildsystemprompt, context_getgitcontext, context_getclaudemd, memory_loadallmemories [EXTRACTED 1.00]
- **Agentic loop core components** — index_runconversationloop, api_streammessage, permissions_classifytoolrisk, permissions_askpermission, compact_shouldautocompact, tools_alltools [INFERRED 0.95]

## Communities (18 total, 3 thin omitted)

### Community 0 - "API & Main Loop"
Cohesion: 0.09
Nodes (34): ApiResponse, createClient(), streamMessage(), estimateTokens(), serializeMessages(), shouldAutoCompact(), smartCompact(), CliArgs (+26 more)

### Community 1 - "Core Functions (cross-module)"
Cohesion: 0.09
Nodes (33): initAgentTool, createClient, streamMessage, truncate, estimateTokens, serializeMessages, shouldAutoCompact, smartCompact (+25 more)

### Community 2 - "Architecture Concepts"
Cohesion: 0.1
Nodes (21): Anthropic SDK (streaming messages API), Claude Code (upstream reference: 512K LOC, 1902 files, ~50 tools), Messages array (client-side state, raw API format), Key takeaway: the model IS the agent; code is just harness for tools+context, nano-claude: ~2,000-line reimplementation of Claude Code, Streaming API (client.messages.stream + content_block_delta events), Convention: tool_result delivered as user-role message, Tool-Use Loop: while(true){API->tool->API} alternates assistant(tool_use)/user(tool_result) (+13 more)

### Community 3 - "Tool Class Hierarchy"
Cohesion: 0.22
Nodes (10): Tool, AGENT_TOOL_NAMES, AgentTool, initAgentTool(), BashTool, EditTool, GlobTool, GrepTool (+2 more)

### Community 4 - "System Prompt & Context"
Cohesion: 0.25
Nodes (13): findProjectRoot(), getClaudeMd(), getGitContext(), tryRun(), handleCommand(), deleteMemory(), ensureDir(), listMemories() (+5 more)

### Community 5 - "Context Window Management"
Cohesion: 0.22
Nodes (10): Context window management (200K tokens, 75% threshold leaves 25% buffer), Smart compaction algorithm: keep last 2 + summarize rest + ack, Sub-agent security boundary: only Read/Glob/Grep, no Bash/Write/Edit, v5: Smart conversation compaction (model summarizes itself at 75% threshold), v7: Sub-agent tool (isolated, read-only, up to 20 turns), estimateTokens() in src/compact.ts, shouldAutoCompact() in src/compact.ts, smartCompact() in src/compact.ts (+2 more)

### Community 6 - "Tool Implementations"
Cohesion: 0.39
Nodes (9): AgentTool, BashTool, EditTool, GlobTool, GrepTool, ReadTool, allTools, Tool (+1 more)

### Community 7 - "CLAUDE.md & Memory"
Cohesion: 0.36
Nodes (8): Multi-level CLAUDE.md: ~/.claude + project root + .claude + cwd (concatenated, gitignore-style), Persistent memory: key-value markdown in ~/.nano-claude/memory/ injected into system prompt, System prompt assembly: CORE_PROMPT + env + git + CLAUDE.md + memories, v6: Multi-level CLAUDE.md + persistent memory, buildSystemPrompt() in src/prompt.ts, getClaudeMd() in src/context.ts, src/context.ts (Git context + multi-level CLAUDE.md), src/memory.ts (Persistent key-value memory)

### Community 8 - "Permission System"
Cohesion: 0.33
Nodes (6): askPermission(), classifyBashRisk(), classifyToolRisk(), DESTRUCTIVE_PATTERNS, RiskLevel, WRITE_PATTERNS

### Community 9 - "Permission Design Rationale"
Cohesion: 0.33
Nodes (6): Design principle: confirmation, not prevention -- user makes dangerous actions a conscious choice, Two-tier risk classification: safe / write / destructive (regex-based), v3: Permission confirmation + Bash safety (two-tier risk classification), askPermission() in src/permissions.ts, classifyBashRisk() in src/permissions.ts, src/permissions.ts (Risk classification + confirmation)

### Community 10 - "Session Persistence"
Cohesion: 0.33
Nodes (6): Sessions saved as raw API message array (no parsing on resume), v4: Session persistence + resume (auto-save raw message array), listSessions() in src/session.ts, loadSession() in src/session.ts, saveSession() in src/session.ts, src/session.ts (Save/load/list sessions)

### Community 11 - "Project Logo (pixel-art)"
Cohesion: 0.6
Nodes (5): Minimalism aesthetic (matches 'nano' / minimal CLI ethos), nano-claude project logo (pixel-art mascot), nano-claude (minimal Claude CLI project), Orange blocky humanoid/creature figure with two black square eyes, 8-bit / pixel-art visual style

### Community 12 - "README & Learning Guide"
Cohesion: 0.5
Nodes (4): nano-claude Learning Guide, nano-claude Learning Guide (Chinese), nano-claude README, nano-claude README (Chinese)

### Community 13 - "Markdown Rendering"
Cohesion: 1.0
Nodes (3): Two-pass rendering: stream raw text then ANSI-erase and re-render, v2: Markdown rendering (two-pass: stream raw then re-render), src/render.ts (Markdown terminal renderer)

### Community 14 - "CLI Args & Print Mode"
Cohesion: 0.67
Nodes (3): Print mode: plain text, no tool noise, auto-allow permissions, exit after model done, v8: CLI args + non-interactive print mode, parseArgs() in src/index.ts

## Knowledge Gaps
- **55 isolated node(s):** `RiskLevel`, `DESTRUCTIVE_PATTERNS`, `WRITE_PATTERNS`, `ApiResponse`, `SESSION_DIR` (+50 more)
  These have ≤1 connection - possible missing edges or undocumented components.
- **3 thin communities (<3 nodes) omitted from report** — run `graphify query` to explore isolated nodes.

## Suggested Questions
_Questions this graph is uniquely positioned to answer:_

- **Why does `runConversationLoop` connect `Core Functions (cross-module)` to `Tool Implementations`?**
  _High betweenness centrality (0.024) - this node is a cross-community bridge._
- **Why does `src/tools/index.ts (Tool registry)` connect `Architecture Concepts` to `Context Window Management`?**
  _High betweenness centrality (0.016) - this node is a cross-community bridge._
- **What connects `RiskLevel`, `DESTRUCTIVE_PATTERNS`, `WRITE_PATTERNS` to the rest of the system?**
  _55 weakly-connected nodes found - possible documentation gaps or missing edges._
- **Should `API & Main Loop` be split into smaller, more focused modules?**
  _Cohesion score 0.09 - nodes in this community are weakly interconnected._
- **Should `Core Functions (cross-module)` be split into smaller, more focused modules?**
  _Cohesion score 0.09 - nodes in this community are weakly interconnected._
- **Should `Architecture Concepts` be split into smaller, more focused modules?**
  _Cohesion score 0.1 - nodes in this community are weakly interconnected._