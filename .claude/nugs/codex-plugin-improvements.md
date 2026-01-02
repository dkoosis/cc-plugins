---
type: task
status: backlog
tags: [codex-plugin, improvements, background-polling]
created: 2026-01-01
---

# Codex Plugin Improvement Opportunities

## High Value

### 1. Remove hardcoded repo (`dkoosis/orca`)
- Detect current repo from `git remote` or make configurable
- Currently blocks anyone else from using the plugin

### 2. Background status polling
Options explored:
- **SessionStart hook**: Check pending tasks when session starts (simplest)
- **UserPromptSubmit hook**: Opportunistic reminder when user is active
- **SubagentStop hook**: React to polling agent completion
- **External poller**: Separate script + file watch
- **MCP server**: Persistent poller exposing `get_codex_updates` tool

Limitation: Claude Code has no persistent daemon architecture. Max agent timeout is 10 min, Codex tasks can take 30 min. Best practical option is SessionStart hook.

### 3. Result ingestion
- Auto-fetch Codex branch when task completes
- Run local tests, provide review before PR creation

### 4. Proactive delegation hook
- `UserPromptSubmit` hook to detect delegation-worthy prompts
- Suggest Codex before user has to think about it

## Medium Value

### 5. Multi-repo support
- Scope task tracking by repo in `.claude/codex-tasks.json`

### 6. CTM templates per task type
- Specialized templates for QA, testing, bug fixes, refactoring

### 7. Learning loop completion
- Post-merge hook to ingest friction reports back into Orca

### 8. Session-start auth check
- `SessionStart` hook to verify Codex login status upfront

## Lower Priority

### 9. Model selection by task
- Haiku for status checks
- Opus for complex delegation decisions

### 10. Resume on failure
- Checkpoint during submission for recovery

### 11. Missing examples file
- `references/ctm-examples.md` is referenced but doesn't exist
