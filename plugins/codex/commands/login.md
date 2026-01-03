---
name: login
description: Navigate to chatgpt.com/codex and ensure logged in
allowed-tools:
  - mcp__plugin_playwright_playwright__browser_navigate
  - mcp__plugin_playwright_playwright__browser_snapshot
  - mcp__plugin_playwright_playwright__browser_wait_for
  - mcp__plugin_playwright_playwright__browser_tabs
  - mcp__plugin_playwright_playwright__browser_close
---

# Codex Login

Ensure the browser is logged into chatgpt.com/codex and ready for task submission.

## Instructions

**IMPORTANT: Check browser state first to avoid opening duplicate tabs.**

1. First, try `browser_snapshot` to check if browser is already open
2. If snapshot succeeds and shows Codex page, skip navigation - already logged in
3. If snapshot fails or shows different page, use `browser_navigate` to go to `https://chatgpt.com/codex`
4. If you get "browser already in use" error, use `browser_tabs` with action "list" to see open tabs
5. Take a browser snapshot to check page state
6. If login page detected (sign in button, email field), inform user they need to log in manually
7. If Codex home page detected ("What should we code next?"), confirm ready
8. Report status to user

## Success Indicators

The Codex home page shows:
- "What should we code next?" heading
- "Describe a task" input field
- Repository selector dropdown
- Tasks/Code reviews/Archive tabs

## Failure Indicators

Login needed if page shows:
- Sign in / Log in buttons
- Email or password fields
- "Welcome to ChatGPT" landing page

## Output

Report one of:
- "Codex ready - logged in and on home page"
- "Login required - please log in to ChatGPT in the browser window"
- "Navigation failed - [error details]"

## Error Handling

- If "browser already in use" error: Do NOT retry navigate. Use `browser_tabs` to manage existing session.
