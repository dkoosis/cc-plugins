---
name: security-fix
description: Fix mechanical Go security lint violations (G115 overflow, G404 weak random). Applies safe patterns from security-patterns skill.
model: sonnet
tools:
  - Read
  - Edit
  - Bash
  - Grep
  - Glob
---

# Security Fix: Mechanical Security Fixes

Fix gosec violations with established security patterns.

## Usage

```
/security-fix [path]
```

**Arguments:**
- `path` - File or directory (default: `./...`)

## What This Fixes

### G115 - Integer Overflow

Add bounds checking for int64 to int conversions.

```go
// Before
count := int(n)

// After
if n < 0 || n > math.MaxInt {
    return fmt.Errorf("count %d overflows int", n)
}
count := int(n)
```

**Strategy:**
- Prefer keeping original type if possible
- Add bounds check when conversion necessary
- Add `math` import if needed

### G404 - Weak Random

Replace math/rand with crypto/rand for security-sensitive code.

```go
// Before
token := fmt.Sprintf("%x", rand.Int63())

// After
b := make([]byte, 8)
if _, err := rand.Read(b); err != nil {
    return "", err
}
token := hex.EncodeToString(b)
```

**Strategy:**
- Update import from `math/rand` to `crypto/rand`
- Handle error from crypto/rand.Read
- May need to add `encoding/hex` or `encoding/base64`

## Workflow

1. Run gosec to find violations:
```bash
golangci-lint run --enable gosec $ARGUMENTS
```

2. Categorize violations by rule

3. Apply fixes:
   - G115: Add bounds checks or change types
   - G404: Switch to crypto/rand with error handling

4. Verify:
```bash
go test ./...
golangci-lint run --enable gosec $ARGUMENTS
```

## Requires Judgment

These need manual review:
- **G101 (hardcoded secrets)** - Needs secrets management strategy
- **G107 (URL variable)** - Needs domain-specific allowlist
- **G304 (file path)** - Needs path validation strategy
- **G401 (weak crypto)** - May affect compatibility

## Output

Report summary when done:

```
## Security Fix Complete

Fixed 8 violations across 4 files:

| Rule | Fixes | Files |
|------|-------|-------|
| G115 | 5 | 3 |
| G404 | 3 | 2 |

Changes:
- Added bounds checks in metrics.go (3 conversions)
- Replaced math/rand with crypto/rand in token.go
- Updated imports in 2 files

Verification:
- go test: passed
- golangci-lint --enable gosec: clean
```
