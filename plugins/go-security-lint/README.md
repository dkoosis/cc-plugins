# go-security-lint

Fix Go security lint violations from gosec (Go Security Checker).

## Target Issues

| Rule | Issue | Severity | Fix |
|------|-------|----------|-----|
| **G115** | Integer overflow on conversion | Medium | Add bounds checking or use safe conversion |
| **G404** | Weak random number generator | Medium | Use `crypto/rand` for security-sensitive code |
| **G101** | Hardcoded credentials | High | Extract to environment/secrets |
| **G107** | URL in http.Get | Low | Validate URL source |
| **G304** | File path from variable | Medium | Sanitize paths |

## Components

### Skill: security-patterns

Knowledge base for Go security patterns:
- Safe integer conversions
- Cryptographic vs pseudo-random usage
- Input sanitization patterns
- Secrets management

### Command: security-fix

Mechanical fixes for common security violations:

```
/security-fix [path]
```

## Usage

### Automatic Fix

```
/security-fix internal/...
```

### With Guidance

When you have gosec violations from `mage qa`:

```
User: "mage qa shows G115 integer overflow warnings"
Claude: [Uses security-patterns skill + security-fix command]
```

## Security Patterns

### G115: Integer Overflow

**Problem:**
```go
func GetSize(n int64) int {
    return int(n)  // G115: potential overflow on 32-bit systems
}
```

**Solution - Bounds Check:**
```go
func GetSize(n int64) (int, error) {
    if n > math.MaxInt || n < math.MinInt {
        return 0, fmt.Errorf("value %d overflows int", n)
    }
    return int(n), nil
}
```

**Solution - Safe Type:**
```go
func GetSize(n int64) int64 {
    return n  // Keep as int64, no conversion needed
}
```

### G404: Weak Random

**Problem:**
```go
import "math/rand"

func GenerateToken() string {
    return fmt.Sprintf("%d", rand.Int())  // G404: weak random
}
```

**Solution:**
```go
import "crypto/rand"

func GenerateToken() (string, error) {
    b := make([]byte, 16)
    if _, err := rand.Read(b); err != nil {
        return "", err
    }
    return hex.EncodeToString(b), nil
}
```

**When math/rand is OK:**
- Shuffling non-security data
- Test data generation
- Simulation/games (add `//nolint:gosec` with reason)

### G101: Hardcoded Credentials

**Problem:**
```go
const apiKey = "sk-abc123..."  // G101: hardcoded credential
```

**Solution:**
```go
func getAPIKey() string {
    return os.Getenv("API_KEY")
}
```

## Quality Targets

- All G115 conversions have bounds checks or use appropriate types
- All security-sensitive random uses crypto/rand
- No hardcoded secrets in source
- Tests pass after changes

## Verification

```bash
golangci-lint run --enable gosec ./...
go test ./...
```
