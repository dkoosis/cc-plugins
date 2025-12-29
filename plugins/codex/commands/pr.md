---
name: pr
description: Create PR from completed Codex task
argument-hint: "[task-id]"
allowed-tools:
  - mcp__plugin_playwright_playwright__browser_navigate
  - mcp__plugin_playwright_playwright__browser_snapshot
  - mcp__plugin_playwright_playwright__browser_click
  - mcp__plugin_playwright_playwright__browser_wait_for
  - Read
  - Write
  - Bash
---

# Create PR from Codex Task

Open a completed Codex task and click "Create PR" to generate a GitHub pull request.

## Arguments

- **task-id** (optional): Specific task ID to create PR for
- If omitted, uses most recent completed task from `.claude/codex-tasks.json`

## Instructions

### Step 1: Identify Target Task

**If task ID provided:**
1. Use the provided task ID directly

**If no task ID:**
1. Read `.claude/codex-tasks.json`
2. Find most recent task with status "complete" (not "merged" or "pr_created")
3. If no complete tasks, inform user and abort

### Step 2: Navigate to Task Detail

1. Navigate to `https://chatgpt.com/codex/tasks/{task_id}`
2. Take snapshot to verify task detail page loaded
3. Verify task shows completion indicators (diff view, line counts)

### Step 3: Verify Task is Complete

Check for completion indicators:
- Diff panel visible with file changes
- "Create PR" button visible in top-right
- No "Working..." indicator

If task not complete:
- Report current status
- Suggest waiting or using `/codex:status`

### Step 4: Create PR

1. Locate "Create PR" button (green GitHub button, top-right area)
2. Click the "Create PR" button
3. Wait for PR creation to complete
4. GitHub may open in new tab or show confirmation

### Step 5: Capture PR URL

After PR creation:
1. Look for PR URL in page or new tab
2. PR URL format: `https://github.com/dkoosis/orca/pull/XXX`
3. If PR opens in new tab, capture the URL

### Step 6: Update Task Record

Update `.claude/codex-tasks.json`:

```json
{
  "id": "task_e_xxx",
  "title": "Task title",
  "status": "pr_created",
  "pr_url": "https://github.com/dkoosis/orca/pull/XXX",
  "pr_created": "2025-01-15T11:30:00Z"
}
```

### Step 7: Report Success

Inform user:
- PR created successfully
- PR URL for review
- Suggest next steps: "Review the PR at [URL] and merge if it adds value"

## PR Review Workflow

After PR is created, user should:
1. Open PR URL in browser
2. Review changes in GitHub
3. If changes add value, merge the PR
4. If changes need work, request changes or close

The plugin does not auto-merge - human review is always required.

## Error Handling

- **Not logged in**: Suggest `/codex:login`
- **Task not found**: Verify task ID, may have been archived
- **Task not complete**: Report status, suggest waiting
- **Create PR button not found**: Task may already have PR, check GitHub
- **PR creation fails**: Take screenshot, report error

## Example Usage

```
/codex:pr                           # Most recent completed task
/codex:pr task_e_694d9631267883     # Specific task
```

## Notes

- Only complete tasks can have PRs created
- Each task can only create one PR
- If PR already exists, Codex may show "View PR" instead of "Create PR"
- PR is created against the branch specified in the task (usually from CTM)
