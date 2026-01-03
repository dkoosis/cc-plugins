---
name: error-fix
description: Fix mechanical Go error handling violations (err113 sentinels, wrapcheck wrapping, errorlint comparisons).
model: haiku
tools:
  - Read
  - Edit
  - Bash
  - Grep
  - Glob
---

# Error Fix: Mechanical Error Handling Fixes

Fix err113, wrapcheck, and errorlint violations mechanically.

## Usage

```
/error-fix [path]
```

**Arguments:**
- `path` - File or directory (default: `./...`)

## What This Fixes

### err113 - Sentinel Error Extraction

Find `errors.New()` in return statements, extract to package-level.

```go
// Before
return errors.New("not found")

// After (at package level)
var errNotFound = errors.New("not found")
// (at return site)
return errNotFound
```

**Naming:**
- Look for existing sentinel naming patterns in file
- Use `errFoo` for unexported, `ErrFoo` for exported
- Semantic names based on message content

### wrapcheck - Error Wrapping

Wrap errors from external packages with context.

```go
// Before
if err != nil {
    return nil, err
}

// After
if err != nil {
    return nil, fmt.Errorf("operation context: %w", err)
}
```

**Context:**
- Use function name or operation as context
- Include relevant IDs when available
- Keep message concise

### errorlint - Comparison Fixes

Replace direct comparison with errors.Is().

```go
// Before
if err == io.EOF {

// After
if errors.Is(err, io.EOF) {
```

## Workflow

1. Run linter to find violations:
```bash
golangci-lint run --enable err113,wrapcheck,errorlint $ARGUMENTS
```

2. Group violations by type and file

3. Apply fixes in order:
   - err113: Extract sentinels (may require adding import)
   - wrapcheck: Add wrapping (may require fmt import)
   - errorlint: Replace comparisons (requires errors import)

4. Verify:
```bash
go test ./...
golangci-lint run --enable err113,wrapcheck,errorlint $ARGUMENTS
```

## Not Covered

These require judgment:
- **Choosing sentinel names** when message is ambiguous
- **Error message rewording** for clarity
- **Deciding export status** of sentinels
- **Complex error chains** needing refactoring

## Output

Report summary when done:

```
## Error Fix Complete

Fixed 15 violations across 6 files:

| Linter | Fixes | Files |
|--------|-------|-------|
| err113 | 8 | 4 |
| wrapcheck | 5 | 3 |
| errorlint | 2 | 2 |

Sentinels created:
- errNotFound (handler.go)
- errInvalidInput (validator.go)
- ErrTimeout (exported, client.go)

Verification:
- go test: passed
- golangci-lint: clean
```
