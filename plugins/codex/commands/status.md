---
name: status
description: Check status of pending Codex tasks
allowed-tools:
  - mcp__plugin_playwright_playwright__browser_navigate
  - mcp__plugin_playwright_playwright__browser_snapshot
  - mcp__plugin_playwright_playwright__browser_click
  - mcp__plugin_playwright_playwright__browser_wait_for
  - Read
  - Write
---

# Check Codex Task Status

Check the status of tasks previously submitted to Codex.

## Instructions

### Step 1: Load Stored Tasks

Read `.claude/codex-tasks.json` to get list of tracked tasks.

If file doesn't exist or is empty, inform user no tasks are being tracked.

### Step 2: Navigate to Codex

1. Navigate to `https://chatgpt.com/codex`
2. Take snapshot to verify on home page
3. If not logged in, abort and suggest `/codex:login`

### Step 3: Check Task List

1. Look at the Tasks tab (should be default view)
2. For each tracked task, search for it in the list by title
3. Check status indicators:
   - **In progress**: No completion indicators, may show "Working..."
   - **Complete**: Shows line change counts (`+123 -45`)
   - **Merged**: Shows "Merged" badge with line counts
   - **Failed**: May show error indicator

### Step 4: Update Stored Status

For each task found, update `.claude/codex-tasks.json`:

```json
{
  "id": "task_e_xxx",
  "title": "Task title",
  "submitted": "2025-01-15T10:30:00Z",
  "source": "issue #123",
  "status": "complete",
  "checked": "2025-01-15T11:00:00Z",
  "lines_added": 123,
  "lines_removed": 45
}
```

### Step 5: Report Status

Present status table to user:

```
| Task | Source | Status | Lines | Submitted |
|------|--------|--------|-------|-----------|
| Add test coverage | #456 | complete | +123 -45 | 30 min ago |
| Fix lint errors | #457 | in progress | - | 10 min ago |
```

If any tasks are complete, suggest:
- "Task X is complete. Run `/codex:pr` to create the PR."

## Status Detection

**Complete indicators:**
- Line change counts visible (`+N -M`)
- Green/red diff numbers
- Task clickable to view details

**In progress indicators:**
- No line counts shown
- May show progress indicator
- "Working for Xm Ys" text

**Merged indicators:**
- "Merged" badge visible
- Purple merge icon

**Not found:**
- Task title not in visible list
- May have been archived
- Check Archive tab if task is old

## Error Handling

- If not logged in: Suggest `/codex:login`
- If task list empty: Report no tasks found
- If stored task not found in UI: Mark as "unknown" status, may be archived
