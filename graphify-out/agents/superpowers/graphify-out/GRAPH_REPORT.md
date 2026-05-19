# Graph Report - /Users/natchung/projects/open-source-trace/agents/superpowers  (2026-05-19)

## Corpus Check
- 90 files · ~90,450 words
- Verdict: corpus is large enough that graph structure adds value.

## Summary
- 438 nodes · 492 edges · 37 communities (31 shown, 6 thin omitted)
- Extraction: 89% EXTRACTED · 11% INFERRED · 0% AMBIGUOUS · INFERRED: 55 edges (avg confidence: 0.85)
- Token cost: 376,267 input · 161,256 output

## Community Hubs (Navigation)
- [[_COMMUNITY_Subagent-Driven Development|Subagent-Driven Development]]
- [[_COMMUNITY_TDD & Systematic Debugging|TDD & Systematic Debugging]]
- [[_COMMUNITY_Brainstorming Skill Core|Brainstorming Skill Core]]
- [[_COMMUNITY_Skill Authoring Best Practices|Skill Authoring Best Practices]]
- [[_COMMUNITY_Worktree & Codex Compatibility|Worktree & Codex Compatibility]]
- [[_COMMUNITY_Skill Design Principles|Skill Design Principles]]
- [[_COMMUNITY_Cross-Harness Tool Mapping|Cross-Harness Tool Mapping]]
- [[_COMMUNITY_OpenCode Support Architecture|OpenCode Support Architecture]]
- [[_COMMUNITY_Brainstorm Server (wsExpress)|Brainstorm Server (ws/Express)]]
- [[_COMMUNITY_Brainstorm Frame Browser API|Brainstorm Frame Browser API]]
- [[_COMMUNITY_Bootstrap Caching Test Helpers|Bootstrap Caching Test Helpers]]
- [[_COMMUNITY_Document Review System|Document Review System]]
- [[_COMMUNITY_Go Fractals Example Plan|Go Fractals Example Plan]]
- [[_COMMUNITY_Svelte Todo Example Plan|Svelte Todo Example Plan]]
- [[_COMMUNITY_Bootstrap Caching Assertions|Bootstrap Caching Assertions]]
- [[_COMMUNITY_render-graphs CLI|render-graphs CLI]]
- [[_COMMUNITY_Skill Improvements (User Feedback)|Skill Improvements (User Feedback)]]
- [[_COMMUNITY_WebSocket Protocol Tests|WebSocket Protocol Tests]]
- [[_COMMUNITY_Brainstorm Client Helpers|Brainstorm Client Helpers]]
- [[_COMMUNITY_Document Review Implementation|Document Review Implementation]]
- [[_COMMUNITY_Superpowers Icon Visual Design|Superpowers Icon Visual Design]]
- [[_COMMUNITY_Superpowers SVG Logo|Superpowers SVG Logo]]
- [[_COMMUNITY_Skill Testing Infrastructure|Skill Testing Infrastructure]]
- [[_COMMUNITY_Windows Polyglot Hooks|Windows Polyglot Hooks]]
- [[_COMMUNITY_render-graphs Internals|render-graphs Internals]]
- [[_COMMUNITY_Visual Brainstorming UX|Visual Brainstorming UX]]
- [[_COMMUNITY_session-start Hook|session-start Hook]]
- [[_COMMUNITY_Code of Conduct|Code of Conduct]]
- [[_COMMUNITY_Bootstrap Assertion Helpers|Bootstrap Assertion Helpers]]
- [[_COMMUNITY_Condition Wait Helpers|Condition Wait Helpers]]
- [[_COMMUNITY_YAGNI Principle|YAGNI Principle]]
- [[_COMMUNITY_Task Filter Functionality|Task: Filter Functionality]]
- [[_COMMUNITY_Task Styling and Polish|Task: Styling and Polish]]
- [[_COMMUNITY_Task End-to-End Tests|Task: End-to-End Tests]]
- [[_COMMUNITY_Task README|Task: README]]
- [[_COMMUNITY_makeOutput function|makeOutput function]]

## God Nodes (most connected - your core abstractions)
1. `Skill: systematic-debugging` - 19 edges
2. `Skill: subagent-driven-development` - 15 edges
3. `Superpowers Release Notes` - 14 edges
4. `subagent-driven-development skill` - 14 edges
5. `Superpowers README` - 12 edges
6. `Go Fractals CLI Implementation Plan` - 12 edges
7. `writing-skills skill` - 11 edges
8. `Persuasion Principles for Skill Design` - 11 edges
9. `Skill: using-git-worktrees` - 10 edges
10. `Svelte Todo Implementation Plan` - 10 edges

## Surprising Connections (you probably didn't know these)
- `Executing-Plans Trigger Prompt` --semantically_similar_to--> `OpenCode Support Implementation Plan`  [INFERRED] [semantically similar]
  tests/skill-triggering/prompts/executing-plans.txt → docs/plans/2025-11-22-opencode-support-implementation.md
- `runTests (ws-protocol)` --calls--> `computeAcceptKey`  [EXTRACTED]
  tests/brainstorm-server/ws-protocol.test.js → skills/brainstorming/scripts/server.cjs
- `runTests (ws-protocol)` --calls--> `encodeFrame`  [EXTRACTED]
  tests/brainstorm-server/ws-protocol.test.js → skills/brainstorming/scripts/server.cjs
- `runTests (ws-protocol)` --calls--> `decodeFrame`  [EXTRACTED]
  tests/brainstorm-server/ws-protocol.test.js → skills/brainstorming/scripts/server.cjs
- `runTests (ws-protocol)` --references--> `OPCODES`  [EXTRACTED]
  tests/brainstorm-server/ws-protocol.test.js → skills/brainstorming/scripts/server.cjs

## Hyperedges (group relationships)
- **Core Basic Workflow Pipeline (brainstorm -> worktree -> plan -> execute -> TDD -> review -> finish)** — skill_brainstorming, skill_using_git_worktrees, skill_writing_plans, skill_subagent_driven_development, skill_test_driven_development, skill_requesting_code_review, skill_finishing_a_development_branch [EXTRACTED 1.00]
- **SDD Two-Stage Review Pattern** — skill_subagent_driven_development, concept_two_stage_code_review, skill_requesting_code_review [EXTRACTED 1.00]
- **AI Contributor Acceptance Gate (rejection rationale + acceptance test + bootstrap loading)** — agents_md_pr_rejection_rationale, agents_md_acceptance_test_react_todo, skill_brainstorming, skill_using_superpowers [INFERRED 0.85]
- **GIT_DIR vs GIT_COMMON detection pattern family** — 20260323codexappcompatibilitydesign_readonlydetection, 20260406worktreerototilldesign_detectstate, 20260323codexappcompatibility_step0detection, 20260323codexappcompatibility_step15detection, 20260406worktreerototill_submoduleguard [INFERRED 0.85]
- **Brainstorm server evolution: build -> non-block refactor -> zero-dep** — 20260117visualbrainstorming_brainstormserver, 20260219visualbrainstormingrefactor_eventsfile, 20260311zerodepbrainstormserver_serverjs [INFERRED 0.95]
- **OpenCode plugin stack (core + plugin + tools + hook)** — 20251122opencodesupportimplementation_skillscore, 20251122opencodesupportimplementation_opencodeplugin, 20251122opencodesupportimplementation_useskilltool, 20251122opencodesupportimplementation_findskillstool, 20251122opencodesupportimplementation_sessionstartedhook [EXTRACTED 1.00]
- **Four Phases of Systematic Debugging** — skill_systematic_debugging_phase1_root_cause, skill_systematic_debugging_phase2_pattern, skill_systematic_debugging_phase3_hypothesis, skill_systematic_debugging_phase4_implementation [EXTRACTED 1.00]
- **Defense-in-Depth Four Layers** — defense_in_depth_layer1_entry, defense_in_depth_layer2_business, defense_in_depth_layer3_environment, defense_in_depth_layer4_debug [EXTRACTED 1.00]
- **Cross-Platform Tool Mapping References** — copilot_tools_doc, codex_tools_doc, gemini_tools_doc, skill_using_superpowers_platform_adaptation [EXTRACTED 1.00]
- **Brainstorm -> Plan -> Subagent Execution Pipeline** — brainstorming_skill, writingplans_skill, subagentdrivendevelopment_skill, external_executing_plans [EXTRACTED 1.00]
- **Per-Task Two-Stage Review Pattern** — implementerprompt_template, specreviewerprompt_template, codequalityreviewerprompt_template, subagentdrivendevelopment_two_stage_review [EXTRACTED 1.00]
- **TDD-for-Documentation Conceptual Set** — writingskills_skill, writingskills_iron_law, writingskills_red_green_refactor, testingskills_subagents_doc, external_tdd_skill [INFERRED 0.95]
- **Condition-based waiting utilities (replace flaky timeouts)** — conditionbasedwaitingexample_waitforevent, conditionbasedwaitingexample_waitforeventcount, conditionbasedwaitingexample_waitforeventmatch [INFERRED 0.95]
- **Zero-dep WebSocket protocol (RFC 6455 handshake + frame codec)** — server_computeacceptkey, server_encodeframe, server_decodeframe, server_opcodes [INFERRED 0.95]
- **Brainstorm reload + event loop (browser helper, server, frame template)** — helper_connect, helper_sendevent, server_encodeframe, server_decodeframe, frametemplate_html [INFERRED 0.85]

## Communities (37 total, 6 thin omitted)

### Community 0 - "Subagent-Driven Development"
Cohesion: 0.07
Nodes (43): Acceptance Test: 'Let's make a react todo list', AGENTS.md Contributor Guidelines, 94% PR Rejection Rate Rationale, CLAUDE.md (symlinked to AGENTS.md), Inline Self-Review Replaces Subagent Review Loops, Concept: Subagent-Driven Development methodology, Two-Stage Code Review (spec compliance then code quality), Prompt: action-oriented (+35 more)

### Community 1 - "TDD & Systematic Debugging"
Cohesion: 0.06
Nodes (40): RED-GREEN-REFACTOR TDD Cycle, Condition-Based Waiting Technique, Wait For Condition vs Sleep Pattern, Skill Bulletproofing Approach, Systematic Debugging Skill Creation Log, Defense-in-Depth Validation, Four-Layer Validation Pattern, Layer 1: Entry Point Validation (+32 more)

### Community 2 - "Brainstorming Skill Core"
Cohesion: 0.06
Nodes (39): Anti-Pattern: This Is Too Simple To Need A Design, Brainstorming Checklist (9 items), Design for Isolation and Clarity, Hard-Gate: No Implementation Before Approval, Brainstorming Key Principles (YAGNI, one-question, multiple choice), brainstorming skill, Spec Self-Review (placeholder/consistency/scope/ambiguity), Visual Companion (browser-based) (+31 more)

### Community 3 - "Skill Authoring Best Practices"
Cohesion: 0.07
Nodes (34): Avoid Deeply Nested References (one level deep), Concise Is Key (context window as public good), Degrees of Freedom (high/medium/low), Anthropic Skill Authoring Best Practices, Build Evaluations First (eval-driven development), Plan-Validate-Execute Pattern, Progressive Disclosure Pattern, Solve, Don't Punt (handle errors in scripts) (+26 more)

### Community 4 - "Worktree & Codex Compatibility"
Cohesion: 0.09
Nodes (24): Step 5 cleanup guard for externally managed worktrees, Detached HEAD handoff payload, Codex App Compatibility Plan, using-git-worktrees Step 0 detection, finishing-a-development-branch Step 1.5 detection, test-environment-detection.sh, Codex App Compatibility Design (PRI-823), Resolve via cd && pwd -P for path normalization (+16 more)

### Community 5 - "Skill Design Principles"
Cohesion: 0.1
Nodes (22): The Description Trap, DOT/GraphViz Flowcharts as Executable Specifications, The 1% Rule for Skill Loading, Skill Consolidation (debug + writing-skills bundling), Instruction Priority Hierarchy (User > Skills > Default), Skills Repository Separation (obra/superpowers-skills), Visual Brainstorming Companion (browser server), Worktree Isolation Required Before Implementation (+14 more)

### Community 6 - "Cross-Harness Tool Mapping"
Cohesion: 0.1
Nodes (21): Copilot CLI Tool Mapping, GEMINI.md (Gemini CLI entrypoint), Gemini CLI Tool Mapping, Gemini @generalist Subagent Pattern, Harness: Claude Code, Harness: Codex App, Harness: Codex CLI, Harness: GitHub Copilot CLI (+13 more)

### Community 7 - "OpenCode Support Architecture"
Cohesion: 0.1
Nodes (21): Code reuse via shared skills-core module, OpenCode Support Design, OpenCode agent system (Build/Plan/subagents), Personal-skill shadowing (personal > superpowers), checkForUpdates function, .codex/superpowers-codex CLI, extractFrontmatter function, findSkillsInDir function (+13 more)

### Community 8 - "Brainstorm Server (ws/Express)"
Cohesion: 0.1
Nodes (21): lib/brainstorm-server (Express+ws+chokidar), helper.js browser auto-capture library, Visual Brainstorming Companion Implementation Plan, /tmp/brainstorm/screen.html shared file, WebSocket -> stdout event relay, Delete wait-for-feedback.sh, .events per-screen file, Selection indicator bar (+13 more)

### Community 9 - "Brainstorm Frame Browser API"
Cohesion: 0.13
Nodes (17): brainstorm API (window.brainstorm), connect (WS reconnect loop), sendEvent, toggleSelect, computeAcceptKey, decodeFrame, encodeFrame, OPCODES (+9 more)

### Community 10 - "Bootstrap Caching Test Helpers"
Cohesion: 0.16
Nodes (14): assert, cleanup(), CONTENT_DIR, fs, http, path, runTests(), SERVER_PATH (+6 more)

### Community 11 - "Document Review System"
Cohesion: 0.18
Nodes (13): Chunk-by-Chunk Review Process, Document Review System Design Spec, Markdown Task Checkbox Syntax, Plan Document Reviewer, Iterative Review Loop Pattern, Spec Document Reviewer, Codex Tool Mapping, Codex multi_agent Feature Config (+5 more)

### Community 12 - "Go Fractals Example Plan"
Cohesion: 0.19
Nodes (13): Cobra CLI dependency, Go Fractals CLI Design, Go Fractals CLI Implementation Plan, Task 10: README, Task 1: Project Setup (go.mod, dirs), Task 2: CLI Framework with Help (Cobra root), Task 3: Sierpinski Algorithm, Task 4: Sierpinski CLI Integration (+5 more)

### Community 13 - "Svelte Todo Example Plan"
Cohesion: 0.24
Nodes (12): Svelte Todo Design, Filter type (all/active/completed), Todo TypeScript interface, Svelte Todo Implementation Plan, Task 1: Project Setup (Vite + Svelte), Task 2: Todo Store, Task 3: localStorage Persistence, Task 4: TodoInput Component (+4 more)

### Community 14 - "Bootstrap Caching Assertions"
Cohesion: 0.18
Nodes (5): afterFirst, afterSecond, firstOutput, result, secondOutput

### Community 15 - "render-graphs CLI"
Cohesion: 0.31
Nodes (7): combineGraphs(), { execSync }, extractDotBlocks(), fs, main(), path, renderToSvg()

### Community 16 - "Skill Improvements (User Feedback)"
Cohesion: 0.25
Nodes (9): Problem 1: Configuration change verification gap, Problem 3: Context bloat in subagent prompts, Skills Improvements from User Feedback, Lean context subagent prompt pattern, Problem 5: Mock-interface drift anti-pattern, Problem 2: Background process accumulation, Problem 6: Code reviewer file access, Problem 4: No self-reflection before handoff (+1 more)

### Community 17 - "WebSocket Protocol Tests"
Cohesion: 0.33
Nodes (4): assert, crypto, path, SERVER_PATH

### Community 18 - "Brainstorm Client Helpers"
Cohesion: 0.33
Nodes (3): eventQueue, indicator, target

### Community 19 - "Document Review Implementation"
Cohesion: 0.47
Nodes (6): Checkbox task syntax for plans, Document Review System Implementation Plan, plan-document-reviewer-prompt template, Writing-plans chunk-by-chunk review loop, spec-document-reviewer-prompt template, Brainstorming spec review loop

### Community 20 - "Superpowers Icon Visual Design"
Cohesion: 0.53
Nodes (6): Atom / Orbit Motif, Central Nucleus (Solid Dot), Superpowers App Icon, Minimalist Black-on-White Design, Diagonal Orbital Arcs, superpowers (project)

### Community 21 - "Superpowers SVG Logo"
Cohesion: 0.4
Nodes (6): 512x512 viewBox (icon-sized), Superpowers Brand Mark, Central Ellipse (nucleus, cx=255.16 cy=258.86), Superpowers Small Logo (SVG), Orbital/Atom-like Motif (Two Curves + Central Ellipse), Superpowers Project

### Community 22 - "Skill Testing Infrastructure"
Cohesion: 0.5
Nodes (5): Testing Superpowers Skills doc, Parse JSONL transcripts (not user output), subagent-driven-development integration test, JSONL session transcript format, analyze-token-usage.py

### Community 23 - "Windows Polyglot Hooks"
Cohesion: 0.6
Nodes (5): cygpath path conversion, Cross-Platform Polyglot Hooks, Pure bash JSON escape function, Polyglot .cmd wrapper (CMD + bash), Reusable run-hook.cmd dispatcher

### Community 24 - "render-graphs Internals"
Cohesion: 0.4
Nodes (5): combineGraphs, extractDotBlocks, extractGraphBody, main (render-graphs), renderToSvg

### Community 26 - "Visual Brainstorming UX"
Cohesion: 0.5
Nodes (4): Browser-vs-Terminal Decision Per Question, Content Fragments Wrapped by Frame Template, Brainstorming Visual Companion Guide, Screen-File / Events-Log Interaction Loop

### Community 27 - "session-start Hook"
Cohesion: 0.5
Nodes (4): escape_for_json, session-start hook, isBootstrapSkillPath, using-superpowers SKILL.md

### Community 28 - "Code of Conduct"
Cohesion: 0.67
Nodes (3): Contributor Covenant Code of Conduct, Enforcement contact jesse@primeradiant.com, Mozilla code of conduct enforcement ladder

### Community 29 - "Bootstrap Assertion Helpers"
Cohesion: 1.0
Nodes (3): assertMissingBootstrap, assertPresentBootstrap, countBootstrapParts

### Community 30 - "Condition Wait Helpers"
Cohesion: 1.0
Nodes (3): waitForEvent, waitForEventCount, waitForEventMatch

## Knowledge Gaps
- **226 isolated node(s):** `assert`, `crypto`, `path`, `SERVER_PATH`, `{ spawn }` (+221 more)
  These have ≤1 connection - possible missing edges or undocumented components.
- **6 thin communities (<3 nodes) omitted from report** — run `graphify query` to explore isolated nodes.

## Suggested Questions
_Questions this graph is uniquely positioned to answer:_

- **Why does `Basic 7-step Workflow` connect `Subagent-Driven Development` to `TDD & Systematic Debugging`, `Cross-Harness Tool Mapping`?**
  _High betweenness centrality (0.056) - this node is a cross-community bridge._
- **Why does `Skill: test-driven-development` connect `TDD & Systematic Debugging` to `Subagent-Driven Development`?**
  _High betweenness centrality (0.044) - this node is a cross-community bridge._
- **Are the 2 inferred relationships involving `Skill: systematic-debugging` (e.g. with `Skill: verification-before-completion` and `Prompt: skill-triggering systematic-debugging`) actually correct?**
  _`Skill: systematic-debugging` has 2 INFERRED edges - model-reasoned connections that need verification._
- **What connects `assert`, `crypto`, `path` to the rest of the system?**
  _226 weakly-connected nodes found - possible documentation gaps or missing edges._
- **Should `Subagent-Driven Development` be split into smaller, more focused modules?**
  _Cohesion score 0.07 - nodes in this community are weakly interconnected._
- **Should `TDD & Systematic Debugging` be split into smaller, more focused modules?**
  _Cohesion score 0.06 - nodes in this community are weakly interconnected._
- **Should `Brainstorming Skill Core` be split into smaller, more focused modules?**
  _Cohesion score 0.06 - nodes in this community are weakly interconnected._