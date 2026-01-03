# go-test-architect

Add high-quality Go tests that catch real bugs and enable safe refactoring.

## Philosophy

Coverage is a discovery metric, not the goal. The real goals are:

1. **Catch regressions** - tests fail when behavior breaks
2. **Document behavior** - tests show how code should be used
3. **Enable safe refactoring** - tests don't break when internals change

**10 tests that assert invariants > 100 tests that check magic strings.**

## Agent

The `go-test-architect` agent analyzes code and creates comprehensive test suites following ADR-008 naming conventions:

```
Test[Component]_[ExpectedBehaviour]_When_[StateUnderTest]
```

Example: `TestValidator_RejectsInput_When_SchemaIsInvalid`

## When to Use

Use this agent after implementing new code that needs test coverage:
- New packages or significant features
- Bug fixes (to prevent regression)
- Code with complex error handling
- Functions with multiple code paths

## What the Agent Does

1. **Analyzes the public contract** - exported functions, methods, types
2. **Identifies invariants** - what must ALWAYS be true
3. **Identifies boundaries** - nil, empty, zero, max, timeout, malformed
4. **Prioritizes error paths** - where bugs hide
5. **Creates table-driven tests** with `t.Run` subtests
6. **Uses black-box testing** (`package foo_test`) by default

## Test Template

```go
// ADR-008: Test[Component]_[ExpectedBehaviour]_When_[StateUnderTest]
package foo_test

func TestComponent_ExpectedBehaviour_When_StateUnderTest(t *testing.T) {
    t.Parallel()

    tests := []struct {
        name    string
        input   foo.Input
        want    foo.Result
        wantErr error
        inspect func(*testing.T, foo.Result)
    }{
        {
            name:    "error: nil input returns ErrInvalidInput",
            input:   nil,
            wantErr: foo.ErrInvalidInput,
        },
        // ... more cases
    }

    for _, tc := range tests {
        tc := tc
        t.Run(tc.name, func(t *testing.T) {
            t.Parallel()
            // ... test body
        })
    }
}
```

## Quality Gates

Tests must pass:
- `go test -race ./...`
- ADR-008 naming convention
- Error paths tested (not just happy path)
- At least one boundary case (nil/empty/malformed)
- Deterministic (no flaky tests)
