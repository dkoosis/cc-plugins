# go-lintbrush

Systematically fix Go lint issues from `golangci-lint` and `mage qa`.

## Prerequisites

This plugin requires **golangci-lint** to be installed:

```bash
# macOS
brew install golangci-lint

# or via go install
go install github.com/golangci/golangci-lint/cmd/golangci-lint@latest
```

Verify installation:
```bash
golangci-lint --version
```

## Components

### Agent: refactor

Handles complexity-related issues requiring judgment:
- **gocyclo** - Cyclomatic complexity reduction
- **funlen** - Function length reduction
- **cyclop** - Package-level complexity
- **nestif** - Nesting depth reduction

Uses Opus model for careful refactoring that preserves behavior.

### Command: polish

Mechanical lint fixes that don't require judgment:
- **godot** - doc comment periods
- **goconst** - magic literals → constants
- **revive** (subset): unused params → `_`, indent-error-flow, error naming

Uses Haiku model for fast, simple fixes.

## Usage

### Complexity Refactoring

When you have complexity violations from `golangci-lint` or `mage qa`:

```
User: "I'm getting gocyclo warnings on internal/server/handler.go"
Claude: [Uses go-lintbrush refactor agent]
```

The agent will:
1. Analyze the function structure
2. Plan refactoring using established patterns
3. Apply changes preserving exact behavior
4. Verify with tests and lint

### Mechanical Fixes

For godot, goconst, or simple revive violations:

```
/polish internal/...
```

This runs a quick pass to fix doc comment formatting, extract magic literals to constants, and clean up simple revive issues.

## Refactoring Patterns

The refactor agent uses these patterns from the complexity reduction knowledge:

| Pattern | Impact | When to Use |
|---------|--------|-------------|
| Guard clauses | Eliminates nesting | Deeply nested conditions |
| Extract switch cases | Reduces cyclop | Large switch statements |
| Extract loop bodies | Linear flow | Complex loop internals |
| Replace boolean soup | Clarity | Complex conditionals |
| Lookup tables | Data-driven | Multiple if/else chains |
| Split responsibilities | Testability | Functions doing multiple things |

## Critical Guardrails

### Semantic Preservation
- Input/output MUST remain identical
- Side effects MUST occur in same order
- Error conditions MUST trigger at same points
- Existing tests MUST pass unchanged

### Code Quality
- Prefer small pure helper functions over clever one-liners
- No panics - always return errors
- No new dependencies
- Preserve error wrapping patterns and logging

### Performance
- O(n) stays O(n)
- Avoid hot-path allocations
- One extra function call is acceptable

## Quality Targets

**Good refactoring:**
- 30%+ complexity reduction
- 2-5 helpers extracted
- Linear happy path
- Pure helper functions (no side effects)

**Excellent refactoring:**
- 50%+ complexity reduction
- Each function <20 lines
- Zero nesting in main function
- Independently testable helpers
- Clear, self-documenting names
