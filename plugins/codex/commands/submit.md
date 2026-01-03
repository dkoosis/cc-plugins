---
name: submit
description: Submit a task to Codex from issue, nug, or description
argument-hint: "<issue-number|nug-id|description>"
allowed-tools:
  - mcp__plugin_playwright_playwright__browser_navigate
  - mcp__plugin_playwright_playwright__browser_snapshot
  - mcp__plugin_playwright_playwright__browser_click
  - mcp__plugin_playwright_playwright__browser_type
  - mcp__plugin_playwright_playwright__browser_wait_for
  - mcp__plugin_playwright_playwright__browser_select_option
  - mcp__plugin_playwright_playwright__browser_evaluate
  - mcp__plugin_playwright_playwright__browser_tabs
  - mcp__plugin_playwright_playwright__browser_close
  - mcp__plugin_playwright_playwright__browser_press_key
  - Bash
  - Read
  - Write
  - Grep
  - mcp__orca__search_nugs
---

# Submit Task to Codex

Generate a Codex Task Manifest (CTM) and submit it to Codex via browser automation.

## Arguments

The argument can be:
- **GitHub issue number**: `#123` or `123` - fetch issue and generate CTM
- **Orca nug ID**: `n:task:something` - generate CTM from nugget
- **Plain description**: Any text - generate CTM from description

## Instructions

### Step 1: Parse Input and Generate CTM

**If GitHub issue number:**
1. Run `gh issue view <number> --json title,body,labels`
2. Extract requirements from issue body
3. Identify relevant paths from issue context
4. Generate CTM using template from codex-prompts skill

**If Orca nug ID:**
1. Search for nugget using `mcp__orca__search_nugs`
2. Extract task description from rationale
3. Use file references if present
4. Generate CTM from nugget context

**If plain description:**
1. Parse description for actionable requirements
2. Infer relevant code areas
3. Generate minimal CTM with clear non-goals

### Step 2: Show CTM for Approval

Display the generated CTM to user and ask for confirmation before submitting.

### Step 3: Navigate to Codex

**IMPORTANT: Check browser state first to avoid opening duplicate tabs.**

1. First, try `browser_snapshot` to check if browser is already open
2. If snapshot succeeds and shows Codex page, skip navigation and proceed
3. If snapshot fails or shows different page, use `browser_navigate` to go to `https://chatgpt.com/codex`
4. If you get "browser already in use" error, use `browser_tabs` with action "list" to see open tabs
5. Take snapshot to verify logged in and on home page
6. If not logged in, abort and inform user

### Step 4: Verify Repository Selection

1. Read `.claude/codex.local.md` to get configured repository (default_repo field)
2. Check repository dropdown shows the configured repo
3. If different repo selected, click dropdown and select correct repo
4. Verify branch is `main` (or appropriate base branch)

### Step 5: Submit Task

1. Click on "Describe a task" input field
2. Type the full CTM content
3. Click submit button (arrow icon)
4. Wait for task to appear in task list (may take a few seconds)

### Step 6: Capture Task ID

1. After submission, the new task should appear at top of task list
2. Click on the task to open detail view
3. Extract task ID from URL: `chatgpt.com/codex/tasks/task_e_[id]`
4. Store task info in `.claude/codex-tasks.json`:

```json
{
  "tasks": [
    {
      "id": "task_e_xxx",
      "title": "Task title from CTM",
      "submitted": "2025-01-15T10:30:00Z",
      "source": "issue #123",
      "status": "pending"
    }
  ]
}
```

### Step 7: Report Success

Inform user:
- Task submitted successfully
- Task ID for reference
- Expected completion time (5-30 minutes)
- How to check status: `/codex:status`

## CTM Template Reference

Use the CTM template from `skills/codex-prompts/references/ctm-template.yml`.

Key sections to populate:
- `task.id`, `task.title`, `task.branch`
- `context.success_definition`
- `context.non_goals`
- `inputs.relevant_paths_hint`
- `testing.commands`

## Error Handling

- If not logged in: Abort and suggest `/codex:login`
- If repo not found: List available repos and ask user
- If submission fails: Take screenshot and report error
- If task ID capture fails: Report task submitted but ID unknown
- If "browser already in use" error: Do NOT retry navigate. Use `browser_tabs` to manage existing session.

## Cleanup

**IMPORTANT: Do NOT close the browser when done.** Leave it open for subsequent commands.
Only close if explicitly requested by user or if browser is in a broken state.

## Example Usage

```
/codex:submit #456
/codex:submit n:task:add-tests-kg
/codex:submit Add test coverage for internal/mcp/server.go
```
