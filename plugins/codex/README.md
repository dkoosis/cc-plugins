# Codex Plugin

Delegate tasks to OpenAI's Codex coding agent via browser automation.

## Overview

This plugin enables Claude Code to delegate appropriate tasks to Codex, OpenAI's coding agent. It uses Playwright to automate the chatgpt.com/codex web interface for task submission, status checking, and PR creation.

## When to Use Codex

**Good for Codex:**
- QA tasks (lint fixes, test coverage, code reviews)
- Clearly-scoped bug fixes with GitHub issues
- Repetitive multi-file changes
- Background work that can run async (5-30 min)

**Keep in Claude:**
- Architecture decisions
- Exploratory work requiring iteration
- Urgent tasks in an active flow
- Work needing conversation/clarification

## Commands

| Command | Description |
|---------|-------------|
| `/codex:submit` | Submit a task to Codex (from issue, nug, or description) |
| `/codex:status` | Check status of pending Codex tasks |
| `/codex:pr` | Create PR from completed Codex task |
| `/codex:login` | Ensure logged in to chatgpt.com/codex |

## Agent

The `codex-delegator` agent proactively suggests delegating to Codex when it recognizes appropriate tasks.

## Configuration

Create `.claude/codex.local.md` in your project with:

```yaml
---
default_repo: owner/repo-name
---
```

Replace `owner/repo-name` with your GitHub repository (e.g., `myorg/myproject`).

Task state is stored in `.claude/codex-tasks.json`.

## Prerequisites

- Playwright MCP plugin enabled
- Logged into chatgpt.com in your browser
- ChatGPT Plus subscription with Codex access

## Installation

```bash
claude --plugin-dir ./codex-plugin
```
