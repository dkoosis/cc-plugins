# CTM Examples

Real-world Codex Task Manifest examples for different task types.

## Bug Fix CTM

For issues with clear reproduction steps and isolated scope.

```yaml
ctm_version: "1.2"

task:
  id: "234"
  title: "Fix nil pointer in config loader"
  repo: "github.com/OWNER/REPO"
  branch: "codex/issue-234-nil-config"
  deliverable: "Open a PR that closes #234"

role:
  primary: "Senior Go engineer working in this repo"
  posture:
    - "Conservative changes"
    - "Fix only the reported bug"

context:
  success_definition:
    - "Config loader handles nil input without panic"
    - "Unit test added covering nil case"
    - "Existing tests still pass"
  non_goals:
    - "No refactoring of unrelated config code"
    - "No changes to config file format"
    - "No new dependencies"

inputs:
  relevant_paths_hint:
    - "internal/config/loader.go"
    - "internal/config/loader_test.go"

constraints:
  go_rules:
    - "Return error instead of panic for nil input"
    - "Add context to error message"

testing:
  commands:
    - "go test ./internal/config/..."
    - "go test -race ./..."

verification:
  must_provide:
    - "Test output showing nil case passes"
    - "Summary of the fix approach"

pr:
  title: "[#234] Fix nil pointer panic in config loader"
```

## QA/Lint Cleanup CTM

For mechanical fixes across multiple files.

```yaml
ctm_version: "1.2"

task:
  id: "lint-unused-imports"
  title: "Remove all unused imports"
  repo: "github.com/OWNER/REPO"
  branch: "codex/lint-unused-imports"
  deliverable: "Open a PR with clean lint output"

role:
  primary: "Code hygiene engineer"
  posture:
    - "Mechanical changes only"
    - "No behavior changes"

context:
  success_definition:
    - "golangci-lint run shows no unused import errors"
    - "All tests pass"
  non_goals:
    - "No other lint categories"
    - "No code reformatting"
    - "No import reorganization beyond removal"

inputs:
  relevant_paths_hint:
    - "internal/"
    - "pkg/"
    - "cmd/"

testing:
  commands:
    - "golangci-lint run --enable goimports ./..."
    - "go test ./..."

verification:
  must_provide:
    - "Before/after lint output"
    - "List of files modified"

pr:
  title: "Remove unused imports across codebase"
```

## Test Coverage CTM

For adding tests to existing code.

```yaml
ctm_version: "1.2"

task:
  id: "test-coverage-auth"
  title: "Add unit tests for auth package"
  repo: "github.com/OWNER/REPO"
  branch: "codex/test-auth-coverage"
  deliverable: "Open a PR with improved test coverage"

role:
  primary: "Test engineer"
  posture:
    - "Focus on behavior coverage, not line coverage"
    - "Test edge cases and error paths"

context:
  success_definition:
    - "Coverage for internal/auth/ increases to >70%"
    - "Error paths are tested"
    - "Edge cases documented in test names"
  non_goals:
    - "No changes to production code"
    - "No mocking external services (use fakes)"

inputs:
  relevant_paths_hint:
    - "internal/auth/"
    - "internal/auth/*_test.go"

constraints:
  test_rules:
    - "Use table-driven tests"
    - "Follow ADR-008 naming: Test[Component]_[Behaviour]_When_[State]"
    - "Use testify/require for setup, testify/assert for verification"

testing:
  commands:
    - "go test -cover ./internal/auth/..."
    - "go test -race ./internal/auth/..."

verification:
  must_provide:
    - "Coverage before and after"
    - "List of test cases added"
    - "Any untested code paths with justification"

pr:
  title: "Add comprehensive test coverage for auth package"
```

## Refactoring CTM

For code improvements with clear scope.

```yaml
ctm_version: "1.2"

task:
  id: "refactor-handler-errors"
  title: "Extract error handling helpers in HTTP handlers"
  repo: "github.com/OWNER/REPO"
  branch: "codex/refactor-handler-errors"
  deliverable: "Open a PR with consolidated error handling"

role:
  primary: "Senior Go engineer"
  posture:
    - "Improve readability without changing behavior"
    - "Preserve all existing functionality"

context:
  success_definition:
    - "Common error handling patterns extracted to helpers"
    - "All handlers use consistent error responses"
    - "No behavior changes in API responses"
  non_goals:
    - "No new error types"
    - "No changes to error messages"
    - "No changes outside handlers package"

inputs:
  relevant_paths_hint:
    - "internal/handlers/"
    - "internal/handlers/*_test.go"

constraints:
  design_rules:
    - "New helpers must be in same package"
    - "Preserve exact HTTP status codes"
    - "Maintain existing log levels"

testing:
  commands:
    - "go test ./internal/handlers/..."
    - "mage qa"

verification:
  must_provide:
    - "List of new helper functions"
    - "Before/after comparison of one handler"
    - "Confirmation no API behavior changed"

pr:
  title: "Consolidate error handling in HTTP handlers"
```

## Usage Tips

1. **Be specific about paths**: The `relevant_paths_hint` dramatically improves Codex navigation
2. **Define non-goals explicitly**: Prevents scope creep
3. **Use concrete success criteria**: "Tests pass" is better than "code works"
4. **Keep deliverable actionable**: "Open a PR" forces end-to-end completion
5. **Match posture to task**: Conservative for fixes, more latitude for new features
