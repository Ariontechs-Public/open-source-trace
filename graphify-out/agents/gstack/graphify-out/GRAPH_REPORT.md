# Graph Report - /Users/natchung/projects/open-source-trace/agents/gstack  (2026-05-19)

## Corpus Check
- Large corpus: 682 files · ~1,257,196 words. Semantic extraction will be expensive (many Claude tokens). Consider running on a subfolder, or use --no-semantic to run AST-only.

## Summary
- 951 nodes · 1496 edges · 61 communities (47 shown, 14 thin omitted)
- Extraction: 95% EXTRACTED · 5% INFERRED · 0% AMBIGUOUS · INFERRED: 76 edges (avg confidence: 0.84)
- Token cost: 778,995 input · 333,852 output

## Community Hubs (Navigation)
- [[_COMMUNITY_OpenAI Config & Design Auth|OpenAI Config & Design Auth]]
- [[_COMMUNITY_pretext.js (vendored)|pretext.js (vendored)]]
- [[_COMMUNITY_browser-skill HN scraper|browser-skill HN scraper]]
- [[_COMMUNITY_Top-level Design Docs|Top-level Design Docs]]
- [[_COMMUNITY_design CLI Args & Brief Parsing|design CLI Args & Brief Parsing]]
- [[_COMMUNITY_make-pdf Rendering|make-pdf Rendering]]
- [[_COMMUNITY_browseClient Tab Functions|browseClient Tab Functions]]
- [[_COMMUNITY_Test Prerequisites|Test Prerequisites]]
- [[_COMMUNITY_gbrain Source Registration|gbrain Source Registration]]
- [[_COMMUNITY_Autoplan  Canary  Codex Skills|Autoplan / Canary / Codex Skills]]
- [[_COMMUNITY_Browser Daemon Architecture|Browser Daemon Architecture]]
- [[_COMMUNITY_Host Adapter Implementations|Host Adapter Implementations]]
- [[_COMMUNITY_OpenClaw CEO Variants|OpenClaw CEO Variants]]
- [[_COMMUNITY_Pre-Landing Review Checklist|Pre-Landing Review Checklist]]
- [[_COMMUNITY_QA Health Score|QA Health Score]]
- [[_COMMUNITY_Worktree + Dedup|Worktree + Dedup]]
- [[_COMMUNITY_browseClient Implementation|browseClient Implementation]]
- [[_COMMUNITY_Classify Cache|Classify Cache]]
- [[_COMMUNITY_design Gallery Generation|design Gallery Generation]]
- [[_COMMUNITY_gbrain Local Status Helpers|gbrain Local Status Helpers]]
- [[_COMMUNITY_design CLI Commands|design CLI Commands]]
- [[_COMMUNITY_design Compare & Serve|design Compare & Serve]]
- [[_COMMUNITY_make-pdf Orchestrator|make-pdf Orchestrator]]
- [[_COMMUNITY_BrowseClient Class|BrowseClient Class]]
- [[_COMMUNITY_design Feedback Roundtrip|design Feedback Roundtrip]]
- [[_COMMUNITY_gbrain Env Builder|gbrain Env Builder]]
- [[_COMMUNITY_PreToolUse Hooks (carefulfreeze)|PreToolUse Hooks (careful/freeze)]]
- [[_COMMUNITY_make-pdf Binary Pipeline|make-pdf Binary Pipeline]]
- [[_COMMUNITY_land-and-deploy + Version Queue|/land-and-deploy + Version Queue]]
- [[_COMMUNITY_GStack Browser + Sidebar Agent|GStack Browser + Sidebar Agent]]
- [[_COMMUNITY_Classify Cache Helpers|Classify Cache Helpers]]
- [[_COMMUNITY_cso Security Audit|/cso Security Audit]]
- [[_COMMUNITY_gstack-upgrade Flow|/gstack-upgrade Flow]]
- [[_COMMUNITY_Model Overlay System|Model Overlay System]]
- [[_COMMUNITY_learn Learnings Log|/learn Learnings Log]]
- [[_COMMUNITY_investigate Root-Cause Flow|/investigate Root-Cause Flow]]
- [[_COMMUNITY_health Quality Dashboard|/health Quality Dashboard]]
- [[_COMMUNITY_browseClient Tests|browseClient Tests]]
- [[_COMMUNITY_Document GenerateRelease (Diataxis)|Document Generate/Release (Diataxis)]]
- [[_COMMUNITY_Context SaveRestore|Context Save/Restore]]
- [[_COMMUNITY_design prototype.ts|design prototype.ts]]
- [[_COMMUNITY_BrowseClientError|BrowseClientError]]
- [[_COMMUNITY_gitleaks Secret Scanning|gitleaks Secret Scanning]]
- [[_COMMUNITY_Skill Manifest Parser|Skill Manifest Parser]]
- [[_COMMUNITY_gstack agents manifest|gstack agents manifest]]
- [[_COMMUNITY_HN HTML Fixture|HN HTML Fixture]]
- [[_COMMUNITY_OpenClaw Retro|OpenClaw Retro]]
- [[_COMMUNITY_plan-tune Self-Tuning|/plan-tune Self-Tuning]]
- [[_COMMUNITY_browse daemon dependency|browse daemon dependency]]
- [[_COMMUNITY_TODOS|TODOS.md]]
- [[_COMMUNITY_readSession helper|readSession helper]]
- [[_COMMUNITY_gstack-memory-helpers|gstack-memory-helpers]]
- [[_COMMUNITY_LocalEngineStatus type|LocalEngineStatus type]]
- [[_COMMUNITY_commands registry|commands registry]]
- [[_COMMUNITY_GenerateOptions interface|GenerateOptions interface]]
- [[_COMMUNITY_PreviewOptions interface|PreviewOptions interface]]

## God Nodes (most connected - your core abstractions)
1. `main()` - 23 edges
2. `BrowseClient` - 22 edges
3. `LazyBrowseClient` - 22 edges
4. `main (design CLI)` - 20 edges
5. `EO()` - 19 edges
6. `printCss()` - 19 edges
7. `requireApiKey()` - 18 edges
8. `c0()` - 17 edges
9. `render()` - 17 edges
10. `ARCHITECTURE.md (master design doc)` - 16 edges

## Surprising Connections (you probably didn't know these)
- `/canary — post-deploy visual monitor (baseline vs live screenshots, console errors)` --semantically_similar_to--> `/devex-review — live DX audit (browse + bash), measures TTHW, scorecard with evidence`  [INFERRED] [semantically similar]
  canary/SKILL.md → devex-review/SKILL.md
- `/design-consultation — propose complete design system, write DESIGN.md` --semantically_similar_to--> `/design-shotgun — generate N AI design variants, side-by-side board, collect feedback`  [INFERRED] [semantically similar]
  design-consultation/SKILL.md → design-shotgun/SKILL.md
- `/design-review — designer's eye QA, iteratively fix and commit per finding` --semantically_similar_to--> `/design-shotgun — generate N AI design variants, side-by-side board, collect feedback`  [INFERRED] [semantically similar]
  design-review/SKILL.md → design-shotgun/SKILL.md
- `/design-review — designer's eye QA, iteratively fix and commit per finding` --semantically_similar_to--> `/devex-review — live DX audit (browse + bash), measures TTHW, scorecard with evidence`  [INFERRED] [semantically similar]
  design-review/SKILL.md → devex-review/SKILL.md
- `/document-release — post-ship docs sync, Diataxis coverage map, CHANGELOG voice polish` --semantically_similar_to--> `/document-generate — Diataxis docs writer (tutorial/how-to/reference/explanation)`  [INFERRED] [semantically similar]
  document-release/SKILL.md → document-generate/SKILL.md

## Hyperedges (group relationships)
- **Design skill cluster (consultation, html, review, shotgun)** — design_consultation_skill, design_html_skill, design_review_skill, design_shotgun_skill [INFERRED 0.85]
- **Documentation skills (generate vs release post-ship)** — document_generate_skill, document_release_skill, diataxis_framework [INFERRED 0.85]
- **Auto-review pipeline participants (autoplan orchestrates codex + design-review + devex-review)** — autoplan_skill, codex_skill, design_review_skill, devex_review_skill [INFERRED 0.85]
- **Model Overlays** — overlay_claude, overlay_opus_4_7, overlay_gpt, overlay_gpt_5_4, overlay_o_series, overlay_gemini [EXTRACTED 1.00]
- **Investigate 4-Phase Pipeline** — investigate_skill, investigate_phases, investigate_3strike_rule, investigate_scope_lock, investigate_debug_report [EXTRACTED 1.00]
- **Safety/Scoping Skill Family** — freeze_skill, guard_skill, careful_skill, unfreeze_skill, investigate_scope_lock [EXTRACTED 1.00]
- **plan-* persona reviewers (autoplan pipeline members)** — plan_ceo_review_skill, plan_eng_review_skill, plan_design_review_skill, plan_devex_review_skill [INFERRED 0.95]
- **OpenClaw skill variants (lightweight forks of main skills for spawned sessions)** — gstack_openclaw_investigate_skill, gstack_openclaw_office_hours_skill, gstack_openclaw_ceo_review_skill, gstack_openclaw_retro_skill [EXTRACTED 1.00]
- **OpenClaw CLAUDE.md injection variants (lite/full/plan dispatch tiers)** — openclaw_gstack_lite_claudemd, openclaw_gstack_full_claudemd, openclaw_gstack_plan_claudemd [EXTRACTED 1.00]
- **Seven Review Specialists (parallel subagents)** — review_specialist_api_contract, review_specialist_testing, review_specialist_performance, review_specialist_maintainability, review_specialist_red_team, review_specialist_data_migration, review_specialist_security [EXTRACTED 1.00]
- **Seven QA Issue Categories** — qa_taxonomy_category_visual_ui, qa_taxonomy_category_functional, qa_taxonomy_category_ux, qa_taxonomy_category_content, qa_taxonomy_category_performance, qa_taxonomy_category_console_errors, qa_taxonomy_category_accessibility [EXTRACTED 1.00]
- **Layered Security Architecture (token + tunnel + cookie + prompt-injection + sanitization)** — architecture_bearer_token_auth, architecture_dual_listener_tunnel, architecture_cookie_security, architecture_prompt_injection_defense, architecture_unicode_sanitization [INFERRED 0.95]
- **gstack Three Core Principles** — principle_boil_the_lake, principle_search_before_building, principle_user_sovereignty [EXTRACTED 1.00]
- **/scrape + /skillify productivity loop (prototype-then-codify)** — skill_scrape, skill_skillify, concept_iron_contract_skillify, concept_match_vs_prototype_path [EXTRACTED 1.00]
- **gbrain Memory Subsystem (setup + sync + memory ingest + trust policy)** — skill_setup_gbrain, skill_sync_gbrain, skill_setup_gbrain_memory_doc, concept_gbrain_memory_integration, concept_per_repo_trust_policy, concept_split_engine_gbrain [EXTRACTED 1.00]
- **Design CLI command dispatch** — cli_main, commands_commands_map, cli_parseargs [INFERRED 0.95]
- **Image generation pipeline** — generate_generate, brief_parsebrief, generate_callimagegeneration [INFERRED 0.95]
- **Comparison board feedback loop** — serve_serve, serve_handlefeedback, serve_handlereload [INFERRED 0.95]
- **GPT-4o vision-based extractors** — diff_diffmockups, memory_extractdesignlanguage, design_to_code_generatedesigntocodeprompt [INFERRED 0.85]
- **Browse client auth resolution chain** — browse_client_resolvebrowseauth, browse_client_defaultstatefile, browse_client_parseintegerenvvalue [INFERRED 0.95]
- **HN HTML parsing helpers** — script_parsestoriesfromhtml, script_striptags, script_decodehtmlentities [EXTRACTED 1.00]
- **Design session lifecycle** — session_createsession, session_readsession, session_updatesession [INFERRED 0.95]
- **Host platform implementations (all conform to HostConfig)** — hosts_claude_claude, hosts_codex_codex, hosts_factory_factory, hosts_kiro_kiro, hosts_opencode_opencode, hosts_slate_slate, hosts_cursor_cursor, hosts_openclaw_openclaw, hosts_hermes_hermes, hosts_gbrain_gbrain [EXTRACTED 1.00]
- **OpenAI-backed design pipeline commands (auth + brief + variants/iterate/evolve/check)** — src_auth_requireapikey, src_variants_variants, src_iterate_iterate, src_evolve_evolve, src_check_checkmockup [INFERRED 0.95]
- **gbrain spawn helpers seed DATABASE_URL via buildGbrainEnv** — lib_gbrain_exec_buildgbrainenv, lib_gbrain_exec_spawngbrain, lib_gbrain_exec_spawngbrainasync, lib_gbrain_exec_execgbraintext, lib_gbrain_exec_execgbrainjson [EXTRACTED 1.00]
- **Memory helpers triad: scan, detect engine, log** — gstack_memory_helpers_secret_scan_file, gstack_memory_helpers_detect_engine_tier, gstack_memory_helpers_with_error_context [INFERRED 0.85]
- **Local engine status classifier (probe + cache + freshClassify)** — gbrain_local_status_local_engine_status, gbrain_local_status_fresh_classify, gbrain_local_status_read_cache [EXTRACTED 1.00]
- **make-pdf generate pipeline (render → load → pdf)** — render_render, browseclient_load_html, browseclient_pdf [EXTRACTED 1.00]
- **HTML assembly: sanitize, smartypants, CSS** — render_sanitize_untrusted_html, smartypants_smartypants, print_css_print_css [EXTRACTED 1.00]
- **Copy-paste CI gate (pdftotext + normalize + assertions)** — pdftotext_pdftotext, pdftotext_normalize, pdftotext_copy_paste_gate [EXTRACTED 1.00]
- **Browse binary resolution (override + sibling + PATH)** — browseclient_resolve_browse_bin, browseclient_resolve_override, browseclient_find_executable [EXTRACTED 1.00]
- **CLI dispatch (parse → command registry → orchestrator)** — cli_main, commands_commands_map, orchestrator_generate [EXTRACTED 1.00]
- **Setup smoke test (browse + pdftotext + generate)** — setup_run_setup, browseclient_resolve_browse_bin, pdftotext_resolve_pdftotext [EXTRACTED 1.00]

## Communities (61 total, 14 thin omitted)

### Community 0 - "OpenAI Config & Design Auth"
Cohesion: 0.06
Nodes (63): ~/.gstack config directory (openai.json secret store), OpenAI Chat Completions API (vision), OpenAI Responses API (image_generation tool), Prompt injection defense (user-feedback tag wrapping + sanitization), CONFIG_PATH, requireApiKey(), resolveApiKey(), saveApiKey() (+55 more)

### Community 1 - "pretext.js (vendored)"
Cohesion: 0.09
Nodes (64): $1(), A0(), aO(), B0(), bO(), c0(), CO(), d0() (+56 more)

### Community 2 - "browser-skill HN scraper"
Cohesion: 0.06
Nodes (17): decodeHtmlEntities(), main(), Output, parseStoriesFromHtml(), Story, stripTags(), FIXTURE, stories (+9 more)

### Community 3 - "Top-level Design Docs"
Cohesion: 0.06
Nodes (54): AGENTS.md — Skill catalog, BROWSER.md — Browser Surface Reference, CLAUDE.md — gstack development conventions, Artifacts Sync (private git repo for cross-machine memory), AskUserQuestion Decision-Brief Format (D<N>, ELI10, Stakes, Recommendation), Browser-Skill Format (frontmatter triggers/host/args, script.ts, fixtures, atomic write), CHANGELOG Voice + Release-Summary Format (GStack/Garry voice), Continuous Checkpoint Mode (WIP commits + [gstack-context]) (+46 more)

### Community 4 - "design CLI Args & Brief Parsing"
Cohesion: 0.05
Nodes (43): briefToPrompt, parseBrief, main (design CLI), parseArgs, printUsage(), printUsage, resolveImagePaths, runSetup (+35 more)

### Community 5 - "make-pdf Rendering"
Cohesion: 0.08
Nodes (46): blockRules(), breakAvoidRules(), chapterRules(), codeRules(), coverRules(), escapeCssString(), figureRules(), footnoteRules() (+38 more)

### Community 6 - "browseClient Tab Functions"
Cohesion: 0.07
Nodes (34): closetab(), findExecutable() [browseClient], js() (eval JS in tab), loadHtml(), browseClient, newtab(), pdf(), resolveBrowseBin() (+26 more)

### Community 7 - "Test Prerequisites"
Cohesion: 0.1
Nodes (28): avail, BROWSE_BIN, EXPECTED, FIXTURE, PDF_BIN, prerequisitesAvailable(), result, ROOT (+20 more)

### Community 8 - "gbrain Source Registration"
Cohesion: 0.09
Nodes (26): EnsureOptions, EnsureResult, ensureSourceRegistered(), SourceState, detectEngineTier(), engineCachePath(), EngineDetect, EngineTier (+18 more)

### Community 9 - "Autoplan / Canary / Codex Skills"
Cohesion: 0.07
Nodes (31): Codex filesystem boundary prompt prefix (don't read skill files), Autoplan 6 decision principles (completeness, boil lakes, pragmatic, DRY, explicit-over-clever, bias-to-action), /autoplan — auto-review pipeline (CEO → Design → Eng → DX), User Challenge: never auto-decided; user's stated direction is the default, Alert on changes vs baseline, not absolute thresholds; require 2+ consecutive checks, /canary — post-deploy visual monitor (baseline vs live screenshots, console errors), DIFF_START/DIFF_END delimiters as prompt-injection defense for adversarial diffs, [P1]/[P2] gate verdict: P1 critical => FAIL, otherwise PASS (+23 more)

### Community 10 - "Browser Daemon Architecture"
Cohesion: 0.07
Nodes (30): Bearer Token Auth (UUID, mode 0600), Command Dispatch (READ/WRITE/META side-effect categories), Cookie Security (Keychain prompt, in-process decrypt, read-only DB), Core idea: persistent browser daemon + opinionated Markdown skills, Daemon Model (long-lived Chromium, state file, port selection, version auto-restart), ARCHITECTURE.md (master design doc), Dual-listener Tunnel Architecture (local vs tunnel port), E2E Session Runner (claude -p subprocess) (+22 more)

### Community 11 - "Host Adapter Implementations"
Cohesion: 0.14
Nodes (18): Host config interface (HostConfig), claude, codex, cursor, factory, gbrain, hermes, ALL_HOST_CONFIGS (+10 more)

### Community 12 - "OpenClaw CEO Variants"
Cohesion: 0.09
Nodes (29): /autoplan pipeline (CEO + eng + design + DX reviews + codex adversarial), CEO Prime Directives (zero silent failures, named errors, shadow paths), Four scope modes: EXPANSION / SELECTIVE / HOLD / REDUCTION, gstack-openclaw-ceo-review (OpenClaw variant of CEO plan review), Iron Law: NO FIXES WITHOUT ROOT CAUSE INVESTIGATION FIRST, gstack-openclaw-investigate (OpenClaw variant of investigate / systematic debugging), 3-strike hypothesis rule (stop after 3 failed hypotheses, escalate), HARD GATE: design-doc-only, no implementation actions (+21 more)

### Community 13 - "Pre-Landing Review Checklist"
Cohesion: 0.1
Nodes (29): Pre-Landing Review Checklist, Fix-First Heuristic (AUTO-FIX vs ASK), Pass 1 CRITICAL (SQL/Race/LLM-trust/Shell-injection/Enum), Pass 2 INFORMATIONAL, Review Suppressions (DO NOT flag list), AI Slop Detection (purple gradients, 3-col grids, centered everything), Design Review Checklist (Lite), Design Confidence Tiers (HIGH/MEDIUM/LOW) (+21 more)

### Community 14 - "QA Health Score"
Cohesion: 0.1
Nodes (21): Diff-Aware Mode (auto on feature branch), Framework-Specific Guidance (Next.js, Rails, WP, SPA), QA Health Score Rubric, Never Fix Bugs Rule (report-only), Quick Mode (30s smoke test), Regression Mode (baseline diff), /qa-only Skill (Report-Only QA Testing), Per-Page Exploration Checklist (+13 more)

### Community 15 - "Worktree + Dedup"
Cohesion: 0.16
Nodes (10): Git worktree isolation for test runs + harvest, copyDirSync(), DedupIndex, getDedupPath(), git(), HarvestResult, loadDedupIndex(), saveDedupIndex() (+2 more)

### Community 16 - "browseClient Implementation"
Cohesion: 0.2
Nodes (18): cleanupPayloadFile(), closetab(), findExecutable(), isExecutable(), js(), JsOptions, loadHtml(), LoadHtmlOptions (+10 more)

### Community 17 - "Classify Cache"
Cohesion: 0.22
Nodes (17): buildCacheKey(), CacheEntry, cacheFilePath(), ClassifyOptions, configFingerprint(), freshClassify(), _gbrainBinCache, gbrainConfigPath() (+9 more)

### Community 18 - "design Gallery Generation"
Cohesion: 0.13
Nodes (13): GalleryOptions, generateEmptyGallery(), generateGalleryHtml(), SessionData, dashIdx, dir, emptyDir, html (+5 more)

### Community 19 - "gbrain Local Status Helpers"
Cohesion: 0.12
Nodes (15): conductor-env-shim, gbrain-local-status, EnsureOptions interface, EnsureResult interface, ensureSourceRegistered(), gbrain-sources (TS helper), probeSource(), SourceState interface (+7 more)

### Community 20 - "design CLI Commands"
Cohesion: 0.17
Nodes (11): generateOptionsFromFlags(), ParsedArgs, previewOptionsFromFlags(), COMMANDS, BrowseClientError, BrowsePdfOptions, ExitCode, FontMode (+3 more)

### Community 21 - "design Compare & Serve"
Cohesion: 0.15
Nodes (11): compare(), CompareOptions, generateCompareHtml(), feedback, goodPath, html, linkPath, newBoard (+3 more)

### Community 22 - "make-pdf Orchestrator"
Cohesion: 0.32
Nodes (6): deriveSlug(), generate(), preview(), ProgressReporter, tmpFile(), tryOpen()

### Community 23 - "BrowseClient Class"
Cohesion: 0.17
Nodes (12): browse (singleton), BrowseClient class, defaultStateFile, LazyBrowseClient, parseIntegerEnvValue, resolveBrowseAuth, decodeHtmlEntities, main (HN script entry) (+4 more)

### Community 24 - "design Feedback Roundtrip"
Cohesion: 0.18
Nodes (8): feedback, feedbackPath, final, html, newBoardPath, newHtml, pending, pendingPath

### Community 25 - "gbrain Env Builder"
Cohesion: 0.29
Nodes (9): DATABASE_URL seeding from ~/.gbrain/config.json, buildGbrainEnv(), BuildGbrainEnvOptions, execGbrainJson(), execGbrainText(), GbrainConfig, spawnGbrain(), spawnGbrainAsync() (+1 more)

### Community 26 - "PreToolUse Hooks (careful/freeze)"
Cohesion: 0.22
Nodes (10): check-careful.sh PreToolUse hook (patterns: rm -rf, DROP, force-push, reset --hard, kubectl delete), /careful — PreToolUse hook that warns before destructive Bash commands, check-freeze.sh PreToolUse hook, Rationale: scope edits during debug prevents accidental fixes to unrelated code, /freeze skill, freeze-dir.txt state file, Rationale: combine careful + freeze hooks for one-command max safety, /guard skill (careful + freeze combo) (+2 more)

### Community 27 - "make-pdf Binary Pipeline"
Cohesion: 0.22
Nodes (9): browse CLI (fast headless browser), make-pdf/dist/pdf binary ($P), Cover/TOC/watermark/curly-quotes/em-dash/page-breaks/tagged-PDF, combined-gate.md fixture (all features on), combined-gate.expected.txt (pdftotext extraction gate), Output contract: stdout=path, stderr=progress, exit codes 0-4, Paged.js print CSS engine, Rationale: copy-paste must produce clean words, not 'S a i l i n g' (+1 more)

### Community 28 - "/land-and-deploy + Version Queue"
Cohesion: 0.28
Nodes (9): design CLI, gstack llms.txt skill index, Takes over after /ship creates the PR, /land-and-deploy skill, ASCII version-queue dashboard with collision/sibling annotations, bin/gstack-next-version utility, Rationale: gh pr list for VERSION numbers across parallel Conductor workspaces, /landing-report skill (read-only queue dashboard) (+1 more)

### Community 29 - "GStack Browser + Sidebar Agent"
Cohesion: 0.22
Nodes (9): browse binary ($B): connect/status/goto/snapshot/focus/disconnect, GStack Browser (rebranded Chromium, headed, anti-bot stealth), Sidebar agent (child Claude instance executing chat requests), Sidebar extension (live activity feed + chat, port 34567), open-gstack-browser skill (launch AI-controlled Chromium with sidebar), Spawned-session mode (OPENCLAW_SESSION=true: no AskUserQuestion, auto-pick), pair-agent skill (pair remote AI agent with your browser via setup key), qa skill (systematic QA test web app + fix bugs iteratively) (+1 more)

### Community 30 - "Classify Cache Helpers"
Cohesion: 0.25
Nodes (5): buildCacheKey(), freshClassify(), localEngineStatus(), readCache(), resolveGbrainBin()

### Community 31 - "/cso Security Audit"
Cohesion: 0.25
Nodes (8): /cso v2 acknowledgements (Sentry, Trail of Bits, Shannon, Snyk, etc.), 8/10 daily confidence gate vs 2/10 comprehensive bar (zero-noise reporting), Infrastructure-first thesis: real attack surface is deps/CI/leaked secrets, not your code, Phase 7 — LLM/AI security checks (prompt injection, unsanitized LLM output, tool calls), Phase 9 — OWASP Top 10 categorical assessment (A01-A10), /cso — Chief Security Officer audit (15 phases, daily vs comprehensive modes), Phase 8 — scan installed skills for malicious patterns (Snyk ToxicSkills inspired), Phase 10 — STRIDE threat modeling per component

### Community 32 - "/gstack-upgrade Flow"
Cohesion: 0.25
Nodes (8): gstack-config binary (settings get/set), Inline upgrade flow (4-option AskUserQuestion or auto-upgrade), Install-type detection (global-git/local-git/vendored), Versioned migration scripts (gstack-upgrade/migrations/v*.sh), Rationale: backup-then-replace with restore-on-fail keeps upgrades safe, /gstack-upgrade skill, Snooze state with escalating backoff (24h/48h/1wk), Team mode (single global source of truth)

### Community 33 - "Model Overlay System"
Cohesion: 0.29
Nodes (8): Claude model overlay, Gemini model overlay, GPT model overlay, GPT-5.4 model overlay (inherits gpt, adds anti-verbosity), {{INHERIT:<base>}} directive for overlay composition, o-series model overlay (reasoning models, hide CoT), Opus 4.7 model overlay (inherits claude), Overlays subordinate to skill workflow / STOP points / AskUserQuestion gates

### Community 34 - "/learn Learnings Log"
Cohesion: 0.29
Nodes (7): gstack-learnings-log binary, gstack-learnings-search binary, Log investigation as learning (gstack-learnings-log), ~/.gstack/projects/$SLUG/learnings.jsonl append-only store, /learn skill (manage project learnings), Staleness check (referenced files deleted) + contradiction check, Subcommands: show / search / prune / export / stats / add

### Community 35 - "/investigate Root-Cause Flow"
Cohesion: 0.29
Nodes (7): 3-strike rule (after 3 failed hypotheses, STOP and escalate), Structured DEBUG REPORT (symptom/root-cause/fix/evidence/regression), gbrain context queries (prior-investigations / project-learnings / eureka), Iron Law: no fixes without root cause investigation first, Known bug-pattern signature table (race/null/state/integration/config/cache), 4-phase flow: investigate / analyze / hypothesize / implement, /investigate skill (systematic debugging)

### Community 36 - "/health Quality Dashboard"
Cohesion: 0.29
Nodes (7): AskUserQuestion tool (MCP variant or native), HARD GATE: never auto-fix issues, report-only, ~/.gstack/projects/$SLUG/health-history.jsonl trend store, Rationale: wrap project's own tools rather than substitute analysis, Weighted composite scoring rubric (typecheck/lint/test/deadcode/shell/gbrain), /health skill (code quality dashboard), ## Health Stack section in CLAUDE.md

### Community 37 - "browseClient Tests"
Cohesion: 0.33
Nodes (4): base, err, found, resolved

### Community 38 - "Document Generate/Release (Diataxis)"
Cohesion: 0.4
Nodes (6): Diataxis framework (4 doc quadrants serving different reader needs), Research-before-write philosophy: survey the whole codebase before any doc line, /document-generate — Diataxis docs writer (tutorial/how-to/reference/explanation), Never clobber CHANGELOG — polish wording only; preserve all entries (real incident hardened), Sell test (Diataxis rubric): CHANGELOG entry must answer what/why-care/how-to-use, /document-release — post-ship docs sync, Diataxis coverage map, CHANGELOG voice polish

### Community 39 - "Context Save/Restore"
Cohesion: 0.4
Nodes (5): Renamed from /checkpoint because Claude Code shadowed the alias as native rewind, Cross-branch resume is the whole point (Conductor workspace handoff), /context-restore — load most recent saved context across all branches, Saved files are append-only; never overwrite (filename YYYYMMDD-HHMMSS = canonical order), /context-save — capture working state (git + decisions + remaining) to checkpoints/

### Community 40 - "design prototype.ts"
Cohesion: 0.67
Nodes (3): briefs, generateMockup(), main()

### Community 41 - "BrowseClientError"
Cohesion: 0.67
Nodes (3): BrowseClientError, BrowseClient.command, Daemon POST /command

## Ambiguous Edges - Review These
- `pretext.js` → `variants.ts`  [AMBIGUOUS]
  design-html/vendor/pretext.js · relation: conceptually_related_to

## Knowledge Gaps
- **318 isolated node(s):** `briefs`, `CallRecord`, `calls`, `future`, `html` (+313 more)
  These have ≤1 connection - possible missing edges or undocumented components.
- **14 thin communities (<3 nodes) omitted from report** — run `graphify query` to explore isolated nodes.

## Suggested Questions
_Questions this graph is uniquely positioned to answer:_

- **What is the exact relationship between `pretext.js` and `variants.ts`?**
  _Edge tagged AMBIGUOUS (relation: conceptually_related_to) - confidence is low._
- **Why does `main()` connect `OpenAI Config & Design Auth` to `design CLI Commands`, `design Compare & Serve`, `make-pdf Orchestrator`, `Test Prerequisites`?**
  _High betweenness centrality (0.055) - this node is a cross-community bridge._
- **Why does `render()` connect `make-pdf Rendering` to `make-pdf Orchestrator`?**
  _High betweenness centrality (0.015) - this node is a cross-community bridge._
- **Are the 13 inferred relationships involving `main()` (e.g. with `checkCommand()` and `compare()`) actually correct?**
  _`main()` has 13 INFERRED edges - model-reasoned connections that need verification._
- **What connects `briefs`, `CallRecord`, `calls` to the rest of the system?**
  _318 weakly-connected nodes found - possible documentation gaps or missing edges._
- **Should `OpenAI Config & Design Auth` be split into smaller, more focused modules?**
  _Cohesion score 0.06 - nodes in this community are weakly interconnected._
- **Should `pretext.js (vendored)` be split into smaller, more focused modules?**
  _Cohesion score 0.09 - nodes in this community are weakly interconnected._