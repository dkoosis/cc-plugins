---
name: godot
description: Fix Go doc comment formatting issues (missing periods, etc.)
model: haiku
tools:
  - Read
  - Edit
  - Bash
  - Grep
  - Glob
---

# Fix Godot Violations

Fix documentation comment formatting issues in Go files.

## Usage

```
/godot [path]
```

**Arguments:**
- `path` - File or directory to fix (defaults to `./...`)

## What This Fixes

### Missing Periods

**Before:**
```go
// Process handles the request
func Process(r *Request) error {
```

**After:**
```go
// Process handles the request.
func Process(r *Request) error {
```

### Comment Formatting

- Ensures exported symbols have doc comments starting with the symbol name
- Ensures comments end with proper punctuation

## Workflow

1. Find godot violations:
```bash
golangci-lint run --enable godot $ARGUMENTS
```

2. For each violation, fix the comment by:
   - Adding period at end if missing
   - Ensuring comment starts with symbol name for exports

3. Verify fix:
```bash
golangci-lint run --enable godot $ARGUMENTS
```

## Rules

- Only fix comment punctuation - don't rewrite content
- Preserve the meaning of the comment
- Don't add doc comments where none exist (that's a different task)
- Focus on mechanical fixes only

## Example Fixes

```go
// Before
// New creates a new client
func New() *Client { ... }

// After
// New creates a new client.
func New() *Client { ... }
```

```go
// Before
// DefaultTimeout is the default
var DefaultTimeout = 30 * time.Second

// After
// DefaultTimeout is the default timeout.
var DefaultTimeout = 30 * time.Second
```

## Batch Mode

To fix all files in a package:

```
/godot internal/server/...
```

Report summary when done:
```
Fixed 12 godot violations in 5 files:
- internal/server/handler.go (4)
- internal/server/client.go (3)
- internal/server/config.go (3)
- internal/server/types.go (2)
```
