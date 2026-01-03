# LLM Counsel Plugin

Seek code and architecture feedback from peer LLMs (ChatGPT, Gemini) via browser automation.

## Overview

This plugin enables Claude Code to request second opinions on important decisions by submitting well-crafted requests to ChatGPT or Gemini. It uses Playwright to automate the web interfaces, then critically assesses responses and discusses worthy improvements with the user.

## When to Use

**Good candidates for external counsel:**
- Architecture decisions (Redis vs in-memory, monolith vs microservices)
- Design pattern choices (factory vs builder, composition vs inheritance)
- Refactoring approaches (how to split a large file, what to extract)
- Code review on complex implementations

**Keep internal:**
- Routine tasks with clear solutions
- Urgent work that can't wait for round-trip
- Tasks requiring Claude's tool access or conversation context

## Commands

| Command | Description |
|---------|-------------|
| `/llm-counsel:ask` | Request feedback on a decision or approach |
| `/llm-counsel:review` | Request code review (max 10 files) |

## Agent

The `counsel-seeker` agent proactively recognizes when Claude is facing an important decision and suggests seeking external feedback (with user permission).

## How It Works

1. **Recognition**: Claude identifies an important decision point
2. **Permission**: Claude suggests seeking counsel, user approves
3. **Formulation**: Claude crafts a well-structured request using project context from Orca
4. **Submission**: Playwright automates ChatGPT or Gemini web UI
5. **Assessment**: Claude critically evaluates the response
6. **Integration**: Claude discusses worthy improvements with user

## Request Style

Requests are crafted to elicit useful feedback:
- A/B decisions or ranked lists (not open-ended)
- Project context framing (goals, priorities)
- Excellence-oriented tone (craftsmanship focus)

## Prerequisites

- Playwright MCP plugin enabled
- Logged into ChatGPT and/or Gemini in your browser
- Orca MCP for project context (recommended)

## Supported Services

- **ChatGPT**: chatgpt.com
- **Gemini**: gemini.google.com
