# Graph Report - /Users/natchung/projects/open-source-trace/cli-tools/claude-code  (2026-05-19)

## Corpus Check
- Large corpus: 118 files · ~569,282 words. Semantic extraction will be expensive (many Claude tokens). Consider running on a subfolder, or use --no-semantic to run AST-only.

## Summary
- 421 nodes · 516 edges · 37 communities (30 shown, 7 thin omitted)
- Extraction: 90% EXTRACTED · 10% INFERRED · 0% AMBIGUOUS · INFERRED: 53 edges (avg confidence: 0.83)
- Token cost: 417,410 input · 178,889 output

## Community Hubs (Navigation)
- [[_COMMUNITY_Command Development Reference|Command Development Reference]]
- [[_COMMUNITY_Hookify Rule Engine (Python)|Hookify Rule Engine (Python)]]
- [[_COMMUNITY_Plugin Manifest & Structure|Plugin Manifest & Structure]]
- [[_COMMUNITY_Hook Development Patterns|Hook Development Patterns]]
- [[_COMMUNITY_Bash Command Validator|Bash Command Validator]]
- [[_COMMUNITY_Plugin-Defined Agents|Plugin-Defined Agents]]
- [[_COMMUNITY_Agent Design Patterns|Agent Design Patterns]]
- [[_COMMUNITY_Hookify Commands|Hookify Commands]]
- [[_COMMUNITY_Hook Python Helpers|Hook Python Helpers]]
- [[_COMMUNITY_Cross-Plugin Commands & Agents|Cross-Plugin Commands & Agents]]
- [[_COMMUNITY_Plugin Conventions|Plugin Conventions]]
- [[_COMMUNITY_Agent Development Schema|Agent Development Schema]]
- [[_COMMUNITY_Issue Auto-Close Scripts|Issue Auto-Close Scripts]]
- [[_COMMUNITY_Issue Lifecycle Scripts|Issue Lifecycle Scripts]]
- [[_COMMUNITY_MDM Deployment Examples|MDM Deployment Examples]]
- [[_COMMUNITY_auto-close-duplicates Module|auto-close-duplicates Module]]
- [[_COMMUNITY_backfill-duplicates Module|backfill-duplicates Module]]
- [[_COMMUNITY_Ralph-Wiggum Loop Plugin|Ralph-Wiggum Loop Plugin]]
- [[_COMMUNITY_Agent SDK Dev Plugin|Agent SDK Dev Plugin]]
- [[_COMMUNITY_Claude Code Repo Metadata|Claude Code Repo Metadata]]
- [[_COMMUNITY_Hook Utility Scripts|Hook Utility Scripts]]
- [[_COMMUNITY_bash_command_validator example|bash_command_validator example]]
- [[_COMMUNITY_Frontend-Design Plugin|Frontend-Design Plugin]]
- [[_COMMUNITY_Commit Workflow|Commit Workflow]]
- [[_COMMUNITY_Explanatory Output Style|Explanatory Output Style]]
- [[_COMMUNITY_Branch Cleanup (clean_gone)|Branch Cleanup (clean_gone)]]
- [[_COMMUNITY_Claude Code Release Notes|Claude Code Release Notes]]
- [[_COMMUNITY_Condition.from_dict helper|Condition.from_dict helper]]
- [[_COMMUNITY_Rule.from_frontmatter helper|Rule.from_frontmatter helper]]
- [[_COMMUNITY_Claude Code Demo GIF|Claude Code Demo GIF]]

## God Nodes (most connected - your core abstractions)
1. `RuleEngine` - 15 edges
2. `Hook Development Skill` - 15 edges
3. `Command Development skill` - 14 edges
4. `Plugin Manifest Reference` - 14 edges
5. `Claude Code Plugins index` - 13 edges
6. `Component Organization Patterns` - 11 edges
7. `MCP Integration Skill` - 11 edges
8. `Command Development README` - 9 edges
9. `Skill Development skill` - 9 edges
10. `Hookify plugin` - 9 edges

## Surprising Connections (you probably didn't know these)
- `bash_command_validator_example.main` --semantically_similar_to--> `main`  [INFERRED] [semantically similar]
  examples/hooks/bash_command_validator_example.py → plugins/security-guidance/hooks/security_reminder_hook.py
- `Command Development skill` --semantically_similar_to--> `Skill Development skill`  [INFERRED] [semantically similar]
  plugins/plugin-dev/skills/command-development/SKILL.md → plugins/plugin-dev/skills/skill-development/SKILL.md
- `load_rules` --semantically_similar_to--> `SECURITY_PATTERNS`  [INFERRED] [semantically similar]
  plugins/hookify/core/config_loader.py → plugins/security-guidance/hooks/security_reminder_hook.py
- `RuleEngine` --uses--> `Rule`  [INFERRED]
  plugins/hookify/core/rule_engine.py → plugins/hookify/core/config_loader.py
- `Plugin: code-review` --semantically_similar_to--> `Plugin: pr-review-toolkit`  [INFERRED] [semantically similar]
  plugins/code-review/README.md → plugins/README.md

## Hyperedges (group relationships)
- **feature-dev specialist agents (explore/design/review)** — agent_code_explorer, agent_code_architect, agent_code_reviewer [EXTRACTED 1.00]
- **plugin-dev validation agents** — agent_agent_creator, agent_plugin_validator, agent_skill_reviewer [EXTRACTED 1.00]
- **MDM deployment template family** — mdm_managed_settings_json, mdm_macos_plist, mdm_macos_mobileconfig, mdm_windows_ps1, mdm_windows_admx [EXTRACTED 1.00]
- **Dynamic command features triad (args + file refs + bash exec)** — concept_arguments_positional, concept_file_ref_at, concept_bash_exec_backtick [EXTRACTED 1.00]
- **Progressive disclosure 3-level loading system** — concept_third_person_description, concept_skill_lean_body, concept_progressive_disclosure [EXTRACTED 1.00]
- **Plugin settings hook integration pattern (file + parse + quick-exit)** — concept_local_md_pattern, concept_frontmatter_extract_sed, concept_quick_exit_pattern [EXTRACTED 1.00]
- **Hook Events forming Claude Code Lifecycle** — hook_event_sessionstart, hook_event_userpromptsubmit, hook_event_pretooluse, hook_event_posttooluse, hook_event_stop, hook_event_sessionend [EXTRACTED 1.00]
- **MCP Server Transport Types** — mcp_server_type_stdio, mcp_server_type_sse, mcp_server_type_http, mcp_server_type_ws [EXTRACTED 1.00]
- **Agent System Prompt Patterns** — agent_pattern_analysis, agent_pattern_generation, agent_pattern_validation, agent_pattern_orchestration [EXTRACTED 1.00]
- **PR Review Toolkit specialized agent suite** — pr_review_toolkit_code_reviewer, pr_review_toolkit_silent_failure_hunter, pr_review_toolkit_code_simplifier, pr_review_toolkit_comment_analyzer, pr_review_toolkit_pr_test_analyzer, pr_review_toolkit_type_design_analyzer, pr_review_toolkit_review_pr_command [EXTRACTED 1.00]
- **Ralph self-referential loop: command + stop hook + completion promise** — ralph_wiggum_ralph_loop_command, ralph_wiggum_stop_hook_rationale, ralph_wiggum_completion_promise_rationale [EXTRACTED 1.00]
- **Agent SDK lifecycle: scaffold then language-specific verification** — agent_sdk_dev_new_sdk_app_command, agent_sdk_dev_verifier_ts, agent_sdk_dev_verifier_py [EXTRACTED 1.00]
- **Hookify rule authoring loop: skill + command + agent** — hookify_command, conversation_analyzer_agent, writing_rules_skill [EXTRACTED 0.95]
- **Hookify rule DSL (event + pattern/conditions + action)** — hookify_event_types, hookify_conditions_operators, hookify_action_warn_block [EXTRACTED 0.95]
- **commit-commands plugin command suite** — commit_command, commit_push_pr_command, clean_gone_command [EXTRACTED 1.00]
- **Hookify event hook entrypoints (all delegate to load_rules + RuleEngine)** — pretooluse_main, posttooluse_main, stop_main, userpromptsubmit_main [EXTRACTED 1.00]
- **Hookify 4-class core: config loader -> rule -> engine -> hook** — config_loader_load_rules, config_loader_rule, rule_engine_ruleengine, pretooluse_main [INFERRED 0.95]
- **GitHub issue lifecycle/cleanup script suite (share lifecycle config)** — sweep_close_expired, lifecycle_comment_script, auto_close_duplicates_main, backfill_duplicate_comments_main, issue_lifecycle_lifecycle [INFERRED 0.95]

## Communities (37 total, 7 thin omitted)

### Community 0 - "Command Development Reference"
Cohesion: 0.06
Nodes (43): Advanced Workflow Patterns reference, Command Documentation Patterns reference, Command Frontmatter reference, Interactive Command Patterns reference, Marketplace Considerations reference, Plugin Command examples, Plugin-Specific Command Features reference, Simple Command examples (+35 more)

### Community 1 - "Hookify Rule Engine (Python)"
Cohesion: 0.07
Nodes (29): Condition, extract_frontmatter(), from_dict(), load_rule_file(), load_rules(), A single condition for matching., Load all hookify rules from .claude directory.      Args:         event: Optiona, Load a single rule file.      Returns:         Rule object or None if file is in (+21 more)

### Community 2 - "Plugin Manifest & Structure"
Cohesion: 0.06
Nodes (42): Advanced Plugin Example (enterprise-devops), Layered Architecture, Manifest field: agents (path), Manifest field: author, Manifest field: commands (path), Manifest field: description, Manifest field: homepage, Manifest field: hooks (+34 more)

### Community 3 - "Hook Development Patterns"
Cohesion: 0.07
Nodes (34): Caching Validation Results, Advanced Hook Use Cases, Multi-Stage Validation, Rate Limiting Pattern, Secret Detection Pattern, Hook Chaining via State (temp files), Hook Development Skill, Env Var: CLAUDE_ENV_FILE (SessionStart) (+26 more)

### Community 4 - "Bash Command Validator"
Cohesion: 0.09
Nodes (31): bash_command_validator_example.main, _validate_command, _VALIDATION_RULES, Condition, Condition.from_dict, extract_frontmatter, load_rule_file, load_rules (+23 more)

### Community 5 - "Plugin-Defined Agents"
Cohesion: 0.1
Nodes (29): Agent: agent-creator, Agent: code-architect, Agent: code-explorer, Agent: code-reviewer (feature-dev), Agent: plugin-validator, Agent: skill-reviewer, Command: /code-review, Command: /plugin-dev:create-plugin (+21 more)

### Community 6 - "Agent Design Patterns"
Cohesion: 0.1
Nodes (23): Agent Example: code-reviewer, Agent Example: docs-generator, Agent Example: security-analyzer, Agent Example: test-generator, Agent Pattern: Analysis Agents, Agent Pattern: Generation Agents, Agent Pattern: Orchestration Agents, Agent Pattern: Validation Agents (+15 more)

### Community 7 - "Hookify Commands"
Cohesion: 0.13
Nodes (18): conversation-analyzer agent, Mine user-frustration signals to seed hook rules, Hookify action: warn vs block, /hookify command, Hookify multi-condition operators (regex_match, contains, equals, not_contains, starts_with, ends_with), /hookify:configure command, Hookify event types (bash|file|stop|prompt|all), Example rule: warn-console-log (+10 more)

### Community 8 - "Hook Python Helpers"
Cohesion: 0.2
Nodes (15): check_patterns(), cleanup_old_state_files(), debug_log(), extract_content_from_input(), get_state_file(), load_state(), main(), Get session-specific state file path. (+7 more)

### Community 9 - "Cross-Plugin Commands & Agents"
Cohesion: 0.18
Nodes (16): /clean_gone (command), /commit (command), /commit-push-pr (command), commit-commands (plugin), code-reviewer (feature-dev agent), code-reviewer (pr-review-toolkit agent), code-simplifier (agent), comment-analyzer (agent) (+8 more)

### Community 10 - "Plugin Conventions"
Cohesion: 0.17
Nodes (15): Auto-discovery: scans commands/*.md, agents/*.md, skills/*/SKILL.md, hooks/hooks.json, .mcp.json, Skill body writing style: imperative/infinitive form, never second person, File naming convention: kebab-case for all plugin files/directories, Manifest path rules: relative, must start with ./, custom paths supplement (not replace) defaults, Plugin directory layout: .claude-plugin/plugin.json (manifest), commands/, agents/, skills/, hooks/, .mcp.json, scripts/, plugin.json manifest: required name (kebab-case), optional version/description/author/repository/license/keywords + component path overrides, Concept: progressive disclosure (metadata/SKILL.md/refs), Skill anatomy: SKILL.md (required) + scripts/, references/, assets/ (optional) (+7 more)

### Community 11 - "Agent Development Schema"
Cohesion: 0.14
Nodes (14): AI-Assisted Agent Generation Template, Agent Creation System Prompt (from Claude Code), Agent Development Skill, Agent frontmatter: color, Agent frontmatter: description with examples, Agent frontmatter: model (inherit/sonnet/opus/haiku), Agent frontmatter: name, Agent frontmatter: tools (least privilege) (+6 more)

### Community 12 - "Issue Auto-Close Scripts"
Cohesion: 0.23
Nodes (13): closeIssueAsDuplicate, extractDuplicateIssueNumber, githubRequest (auto-close), autoCloseDuplicates, githubRequest (backfill), backfillDuplicateComments, triggerDedupeWorkflow, lifecycle (+5 more)

### Community 13 - "Issue Lifecycle Scripts"
Cohesion: 0.24
Nodes (9): lifecycle, LifecycleLabel, DRY_RUN, entry, CLOSE_MESSAGE(), closeExpired(), DRY_RUN, githubRequest() (+1 more)

### Community 14 - "MDM Deployment Examples"
Cohesion: 0.24
Nodes (10): com.anthropic.claudecode.mobileconfig (macOS), com.anthropic.claudecode.plist (macOS), managed-settings.json template, MDM Deployment Examples, ClaudeCode.admx + adml (Group Policy), Set-ClaudeCodePolicy.ps1 (Intune), settings-bash-sandbox.json, settings-lax.json (+2 more)

### Community 15 - "auto-close-duplicates Module"
Cohesion: 0.39
Nodes (7): autoCloseDuplicates(), closeIssueAsDuplicate(), extractDuplicateIssueNumber(), GitHubComment, GitHubIssue, GitHubReaction, githubRequest()

### Community 16 - "backfill-duplicates Module"
Cohesion: 0.53
Nodes (5): backfillDuplicateComments(), GitHubComment, GitHubIssue, githubRequest(), triggerDedupeWorkflow()

### Community 17 - "Ralph-Wiggum Loop Plugin"
Cohesion: 0.47
Nodes (6): /cancel-ralph (command), Completion promise tag <promise>TEXT</promise> signals exit; max-iterations as safety net, /help (ralph-wiggum command), ralph-wiggum (plugin), /ralph-loop (command), Use Stop hook to intercept Claude exit and feed same prompt back as self-referential loop

### Community 18 - "Agent SDK Dev Plugin"
Cohesion: 0.53
Nodes (6): /new-sdk-app (command), Ask clarifying questions one at a time to ease user response burden, agent-sdk-dev (plugin), agent-sdk-verifier-py (agent), agent-sdk-verifier-ts (agent), Do not consider setup complete until code passes type-check / syntax verification

### Community 19 - "Claude Code Repo Metadata"
Cohesion: 0.33
Nodes (6): Claude Code: agentic coding tool in the terminal, Claude Code License (Anthropic Commercial Terms), Claude Code README, Claude Code Security Policy (HackerOne), npm install deprecated; prefer curl/Homebrew/winget, plugins/ directory: bundled Claude Code plugins

### Community 20 - "Hook Utility Scripts"
Cohesion: 0.5
Nodes (4): hook-linter.sh, test-hook.sh, validate-hook-schema.sh, Hook Development Utility Scripts

### Community 22 - "Frontend-Design Plugin"
Cohesion: 0.67
Nodes (3): Avoid generic AI aesthetics (Inter, purple gradients) by committing to a bold aesthetic direction, frontend-design (plugin), frontend-design (skill)

### Community 23 - "Commit Workflow"
Cohesion: 0.67
Nodes (3): /commit command, Commit-commands batch-tool-call workflow, /commit-push-pr command

## Knowledge Gaps
- **178 isolated node(s):** `Append debug message to log file with timestamp.`, `Get session-specific state file path.`, `Remove state files older than 30 days.`, `Load the state of shown warnings from file.`, `Save the state of shown warnings to file.` (+173 more)
  These have ≤1 connection - possible missing edges or undocumented components.
- **7 thin communities (<3 nodes) omitted from report** — run `graphify query` to explore isolated nodes.

## Suggested Questions
_Questions this graph is uniquely positioned to answer:_

- **Why does `Advanced Plugin Example (enterprise-devops)` connect `Plugin Manifest & Structure` to `Hook Development Patterns`?**
  _High betweenness centrality (0.036) - this node is a cross-community bridge._
- **Why does `Skill Development skill` connect `Plugin Conventions` to `Command Development Reference`?**
  _High betweenness centrality (0.025) - this node is a cross-community bridge._
- **Why does `Pattern: Security Validation` connect `Hook Development Patterns` to `Plugin Manifest & Structure`?**
  _High betweenness centrality (0.025) - this node is a cross-community bridge._
- **Are the 6 inferred relationships involving `RuleEngine` (e.g. with `Rule` and `Condition`) actually correct?**
  _`RuleEngine` has 6 INFERRED edges - model-reasoned connections that need verification._
- **What connects `Append debug message to log file with timestamp.`, `Get session-specific state file path.`, `Remove state files older than 30 days.` to the rest of the system?**
  _178 weakly-connected nodes found - possible documentation gaps or missing edges._
- **Should `Command Development Reference` be split into smaller, more focused modules?**
  _Cohesion score 0.06 - nodes in this community are weakly interconnected._
- **Should `Hookify Rule Engine (Python)` be split into smaller, more focused modules?**
  _Cohesion score 0.07 - nodes in this community are weakly interconnected._