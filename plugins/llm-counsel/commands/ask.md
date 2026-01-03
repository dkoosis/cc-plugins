---
name: ask
description: Request feedback from ChatGPT or Gemini on a decision or approach
argument-hint: "[question or topic] [--chatgpt|--gemini]"
allowed-tools:
  - mcp__plugin_playwright_playwright__browser_navigate
  - mcp__plugin_playwright_playwright__browser_snapshot
  - mcp__plugin_playwright_playwright__browser_click
  - mcp__plugin_playwright_playwright__browser_type
  - mcp__plugin_playwright_playwright__browser_wait_for
  - mcp__plugin_playwright_playwright__browser_tabs
  - mcp__plugin_playwright_playwright__browser_close
  - mcp__plugin_playwright_playwright__browser_press_key
  - Read
  - Grep
  - mcp__orca__search_nugs
---

# Ask for External Feedback

Request feedback from ChatGPT or Gemini on a decision, approach, or design question.

## Arguments

- **question/topic**: The decision or question to get feedback on
- **--chatgpt**: Use ChatGPT (default if not specified)
- **--gemini**: Use Gemini

## Instructions

### Step 1: Parse Input

Extract the question/topic and service selection from arguments.
- If `--gemini` specified, use Gemini
- Otherwise default to ChatGPT

### Step 2: Gather Context

1. Search Orca for relevant project context: `mcp__orca__search_nugs`
2. Identify project goals, constraints, relevant decisions
3. If question references code, read relevant files

### Step 3: Formulate Request

Craft a well-structured request:

```
[Project context - 2-3 sentences about goals/priorities]

[The specific question from user]

Please provide:
- A clear recommendation with rationale, OR
- A ranked list of approaches with tradeoffs

Our goal is ἀρετή. Please bring your 職人気質.
```

Frame as A/B decision or ranked list, not open-ended.

### Step 4: Show Request for Approval

Display the formulated request to user before submitting.
Ask: "Ready to submit this to [ChatGPT/Gemini]?"

### Step 5: Navigate to Service

**IMPORTANT: Check browser state first to avoid duplicate tabs.**

1. Try `browser_snapshot` to check if browser is already open
2. If snapshot succeeds and shows target service, proceed
3. If snapshot fails or shows different page, navigate:
   - ChatGPT: `https://chatgpt.com`
   - Gemini: `https://gemini.google.com`
4. If "browser already in use" error: Use `browser_tabs` to manage session
5. Verify logged in (chat input visible)
6. If not logged in, inform user and abort

### Step 6: Submit Request

1. Click on chat input field
2. Type the formulated request
3. Submit (Enter or click send)
4. Wait for response to complete (watch for typing indicator to stop)

### Step 7: Capture Response

1. Read the response text from the page
2. Take snapshot to capture full response if needed

### Step 8: Present Assessment

Return to user with:

```
## Feedback from [ChatGPT/Gemini]

**Their Response:**
[Summary of key points]

**My Assessment:**
[Your critical evaluation]

**Recommendation:**
[Synthesized path forward]
```

## Error Handling

- **Not logged in**: "Please log in to [service] in the browser window"
- **Browser issues**: Use `browser_tabs` to manage, don't retry navigate blindly
- **Service error**: Report and offer to try alternative service

## Cleanup

**Do NOT close the browser when done.** Leave open for subsequent commands.

## Example Usage

```
/llm-counsel:ask Should we use Redis or in-memory caching?
/llm-counsel:ask --gemini Best approach to split this 800-line file
/llm-counsel:ask --chatgpt Factory vs builder pattern for this use case
```
