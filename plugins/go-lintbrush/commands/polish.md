---
name: polish
description: Fix mechanical Go lint issues (godot, goconst, simple revive violations). For complexity issues, use the refactor agent instead.
model: haiku
tools:
  - Read
  - Edit
  - Bash
  - Grep
  - Glob
---

# Polish: Mechanical Lint Fixes

Fix low-risk, mechanical lint issues that don't require architectural judgment.

## Usage

```
/polish [linter] [path]
```

**Arguments:**
- `linter` - Optional: godot, goconst, revive, or all (default: all)
- `path` - File or directory (default: `./...`)

## What This Fixes

### godot - Doc Comment Formatting

Missing periods at end of exported symbol comments.

```go
// Before
// Process handles the request
func Process(r *Request) error {

// After
// Process handles the request.
func Process(r *Request) error {
```

### goconst - Magic Literals

Repeated string/int literals that should be constants.

```go
// Before
if status == "pending" { ... }
if status == "pending" { ... }

// After
const statusPending = "pending"
if status == statusPending { ... }
```

**Rules:**
- Place const near first meaningful use
- Names reflect domain semantics
- Keep unexported unless already used externally
- Skip test-only literals (add `//nolint:goconst` with reason)

### revive - Mechanical Subset

#### unused-parameter
Rename unused params to `_` (preserves signatures).

```go
// Before
func handle(w http.ResponseWriter, r *http.Request) {
    w.Write([]byte("ok"))  // r unused
}

// After
func handle(w http.ResponseWriter, _ *http.Request) {
    w.Write([]byte("ok"))
}
```

#### indent-error-flow
Remove `else` after `return`.

```go
// Before
if err != nil {
    return err
} else {
    doWork()
}

// After
if err != nil {
    return err
}
doWork()
```

#### error-naming
Sentinel errors should be `errFoo` not `sentinel`.

```go
// Before
var sentinel = errors.New("not found")

// After
var errNotFound = errors.New("not found")
```

## Workflow

1. Run linter to find violations:
```bash
golangci-lint run --enable godot,goconst,revive $ARGUMENTS
```

2. Group violations by type and file

3. Apply fixes:
   - godot: Add periods to doc comments
   - goconst: Extract constants with semantic names
   - revive/unused-parameter: Rename to `_`
   - revive/indent-error-flow: Remove else blocks
   - revive/error-naming: Rename to `errX` format

4. Verify:
```bash
go test ./...
golangci-lint run $ARGUMENTS
```

## Not Covered

These require judgment and should use the `refactor` agent or manual review:

- **Complexity issues** (gocyclo, funlen, nestif) → use `/refactor`
- **Missing doc comments** (adding new comments) → needs human judgment
- **Type renames** (stutter fixes) → affects API, needs review
- **contextcheck** → threading ctx requires understanding call chains

## Output

Report summary when done:

```
## Polish Complete

Fixed 23 violations across 8 files:

| Linter | Fixes | Files |
|--------|-------|-------|
| godot  | 12    | 5     |
| goconst | 4    | 2     |
| revive/unused-param | 5 | 3 |
| revive/indent-flow | 2 | 1 |

Verification:
- go test: passed
- golangci-lint: clean
```
