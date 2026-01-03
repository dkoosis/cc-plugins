---
name: review
description: Request code review from ChatGPT or Gemini (max 10 files)
argument-hint: "<file-paths...> [--chatgpt|--gemini]"
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
  - Glob
  - mcp__orca__search_nugs
---

# Request Code Review

Request a code review from ChatGPT or Gemini on specific files (maximum 10 files).

## Arguments

- **file-paths**: One or more file paths to review (max 10)
- **--chatgpt**: Use ChatGPT (default if not specified)
- **--gemini**: Use Gemini

## Instructions

### Step 1: Parse Input and Validate

1. Extract file paths and service selection from arguments
2. Expand any glob patterns
3. **Enforce limit**: If more than 10 files, inform user and ask them to narrow scope
4. Verify all files exist

### Step 2: Gather Context

1. Search Orca for project context: `mcp__orca__search_nugs`
   - Project goals and priorities
   - Code style preferences
   - Known constraints or patterns
2. Read each file to review
3. Note any relationships between files

### Step 3: Formulate Review Request

Craft a well-structured code review request:

```
[Project context - goals, priorities, e.g., "Security and scalability are top priorities" or "This is a personal tool where speed and low friction are paramount"]

Please review the following code and provide a ranked list of corrections and recommendations.

Our goal is ἀρετή. Please bring your 職人気質.

---

**File 1: [path]**
```[language]
[file contents]
```

**File 2: [path]**
```[language]
[file contents]
```

[... up to 10 files ...]

---

Focus on:
- Correctness and potential bugs
- Design and architecture concerns
- Security considerations
- Performance implications
- Code clarity and maintainability

Provide a ranked list from most to least critical.
```

### Step 4: Show Request for Approval

Display summary to user:
- Files to review (list)
- Service to use
- Context framing

Ask: "Ready to submit this review request to [ChatGPT/Gemini]?"

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
2. Type the formulated review request
3. Submit (Enter or click send)
4. Wait for response to complete (may take longer for code review)

### Step 7: Capture Response

1. Read the response text from the page
2. Take snapshot to capture full response if needed
3. Parse ranked recommendations from response

### Step 8: Present Assessment

Return to user with:

```
## Code Review from [ChatGPT/Gemini]

**Files Reviewed:** [count] files

**Their Findings (ranked):**
1. [Most critical issue]
2. [Second issue]
3. [...]

**My Assessment:**
[Your critical evaluation of their findings]
- Agree with: [which points]
- Disagree/modify: [which points and why]
- Additional concerns: [anything they missed]

**Recommended Actions:**
[Prioritized list of what to actually do]
```

## Error Handling

- **Too many files**: "Please limit to 10 files. You specified [N] files."
- **File not found**: Report which files couldn't be read
- **Not logged in**: "Please log in to [service] in the browser window"
- **Browser issues**: Use `browser_tabs` to manage, don't retry navigate blindly
- **Response too long/truncated**: Note limitation, focus on captured findings

## Cleanup

**Do NOT close the browser when done.** Leave open for subsequent commands.

## Example Usage

```
/llm-counsel:review internal/server/handler.go
/llm-counsel:review --gemini pkg/api/*.go
/llm-counsel:review src/auth.ts src/session.ts --chatgpt
```
