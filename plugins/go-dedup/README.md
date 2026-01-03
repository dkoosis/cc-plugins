# go-dedup

Analyze and consolidate duplicate Go code detected by jscpd.

## Prerequisites

This plugin requires **jscpd** (copy/paste detector) to be installed:

```bash
npm install -g jscpd
```

Verify installation:
```bash
jscpd --version
```

## Overview

Code duplication creates maintenance burden - bugs must be fixed in multiple places, and changes ripple across the codebase. This plugin helps identify duplication patterns and suggests appropriate consolidation strategies.

## Components

### Agent: dedup-analyze

Analyzes jscpd output and proposes consolidation strategies. Uses Opus for careful refactoring decisions.

### Skill: dedup-patterns

Knowledge base for deduplication strategies:
- When to extract helpers vs leave duplicated
- Interface extraction patterns
- Generics usage for type-safe consolidation

## Usage

### Analyze Duplication

When `mage qa` reports code clones:

```
User: "mage qa shows 15 code clones, can you help consolidate?"
Claude: [Uses go-dedup agent to analyze and propose fixes]
```

### Run jscpd Directly

```bash
jscpd --format go --reporters json ./...
```

## Deduplication Strategies

### Strategy 1: Extract Helper Function

When duplicated code performs the same operation with different inputs.

**Before:**
```go
// File A
for _, item := range itemsA {
    if err := validate(item); err != nil { continue }
    result := transform(item)
    if err := save(result); err != nil { return err }
}

// File B (nearly identical)
for _, item := range itemsB {
    if err := validate(item); err != nil { continue }
    result := transform(item)
    if err := save(result); err != nil { return err }
}
```

**After:**
```go
// internal/processing/items.go
func ProcessItems(items []Item) error {
    for _, item := range items {
        if err := validate(item); err != nil { continue }
        result := transform(item)
        if err := save(result); err != nil { return err }
    }
    return nil
}
```

### Strategy 2: Extract Interface

When duplicated code operates on different types with same behavior.

**Before:**
```go
func SaveUser(u *User) error { /* marshal, write, log */ }
func SaveOrder(o *Order) error { /* marshal, write, log */ }
```

**After:**
```go
type Saveable interface {
    TableName() string
    Validate() error
}

func Save[T Saveable](item T) error {
    // Single implementation
}
```

### Strategy 3: Accept Duplication

Sometimes duplication is correct:
- Different evolution paths expected
- Coupling would be artificial
- Code is simple enough that abstraction adds complexity

Add comment: `// NOTE: Intentional duplication - X and Y evolve independently`

## Quality Targets

| Metric | Before | Target |
|--------|--------|--------|
| Clone count | 15+ | <5 |
| Clone % | >5% | <2% |
| Shared code in right package | N/A | ✓ |

## Anti-Patterns

### DON'T
- Create god packages with all shared code
- Over-abstract for hypothetical reuse
- Break encapsulation to share implementation
- Force coupling between unrelated domains

### DO
- Keep shared code near its primary users
- Prefer duplication over wrong abstraction
- Use generics for type-safe consolidation
- Document intentional duplication
