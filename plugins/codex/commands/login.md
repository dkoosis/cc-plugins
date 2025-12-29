---
name: login
description: Navigate to chatgpt.com/codex and ensure logged in
allowed-tools:
  - mcp__plugin_playwright_playwright__browser_navigate
  - mcp__plugin_playwright_playwright__browser_snapshot
  - mcp__plugin_playwright_playwright__browser_wait_for
---

# Codex Login

Ensure the browser is logged into chatgpt.com/codex and ready for task submission.

## Instructions

1. Navigate to `https://chatgpt.com/codex`
2. Take a browser snapshot to check page state
3. If login page detected (sign in button, email field), inform user they need to log in manually
4. If Codex home page detected ("What should we code next?"), confirm ready
5. Report status to user

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
