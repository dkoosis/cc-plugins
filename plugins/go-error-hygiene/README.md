# go-error-hygiene

Fix Go error handling lint violations for idiomatic error patterns.

## Target Linters

| Linter | Issue | Fix |
|--------|-------|-----|
| **err113** | Dynamic errors in returns | Extract to sentinel errors |
| **wrapcheck** | Unwrapped external errors | Add `fmt.Errorf("context: %w", err)` |
| **errorlint** | `err == ErrFoo` comparisons | Use `errors.Is(err, ErrFoo)` |

## Components

### Skill: error-patterns

Knowledge base for Go error handling idioms:
- Sentinel error declaration patterns
- Error wrapping best practices
- errors.Is/As usage guidelines

### Command: error-fix

Mechanical fixes for error handling violations:

```
/error-fix [path]
```

Applies:
- Extract dynamic errors to package-level sentinels
- Wrap external package errors with context
- Replace `==` with `errors.Is()`

## Usage

### Automatic Fix

```
/error-fix internal/server/...
```

### With Guidance

When you have err113/wrapcheck/errorlint violations from `mage qa`:

```
User: "mage qa is failing with err113 on handler.go"
Claude: [Uses error-patterns skill + error-fix command]
```

## Error Patterns

### Sentinel Errors (err113)

**Before:**
```go
func Parse(s string) (int, error) {
    if s == "" {
        return 0, errors.New("empty input")  // err113 violation
    }
    // ...
}
```

**After:**
```go
var ErrEmptyInput = errors.New("empty input")

func Parse(s string) (int, error) {
    if s == "" {
        return 0, ErrEmptyInput
    }
    // ...
}
```

### Error Wrapping (wrapcheck)

**Before:**
```go
func LoadConfig(path string) (*Config, error) {
    data, err := os.ReadFile(path)
    if err != nil {
        return nil, err  // wrapcheck: not wrapped
    }
    // ...
}
```

**After:**
```go
func LoadConfig(path string) (*Config, error) {
    data, err := os.ReadFile(path)
    if err != nil {
        return nil, fmt.Errorf("load config %s: %w", path, err)
    }
    // ...
}
```

### Error Comparison (errorlint)

**Before:**
```go
if err == io.EOF {  // errorlint: use errors.Is
    break
}
```

**After:**
```go
if errors.Is(err, io.EOF) {
    break
}
```

## Quality Targets

- All err113 violations resolved with semantic sentinel names
- All external errors wrapped with meaningful context
- All error comparisons use errors.Is/errors.As
- Tests pass after changes
