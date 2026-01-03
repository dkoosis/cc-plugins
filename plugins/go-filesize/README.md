# go-filesize

Manage Go source file sizes and plan strategic file splits.

## Overview

Large files (>500 LOC) become hard to navigate and maintain. This plugin helps identify oversized files and plan splits that improve organization without fragmenting cohesive code.

## Size Tiers

| Tier | Lines | Action |
|------|-------|--------|
| **Green** | <500 | No action needed |
| **Yellow** | 500-999 | Monitor, consider split |
| **Red** | ≥1000 | Split recommended |

## Components

### Command: filesize-report

Generate a report of file sizes with recommendations:

```
/filesize-report [path]
```

### Agent: file-split

Plan and execute strategic file splits. Uses Opus for careful refactoring.

### Skill: file-splitting

Knowledge base for file organization:
- When to split vs keep together
- Naming conventions for split files
- Maintaining package cohesion

## Usage

### Get Report

```
/filesize-report internal/...
```

Output:
```
## File Size Report

🔴 Red (16 files >1000 LOC)
  2563  internal/lsp/tslsp_handler.go
  2526  internal/kg/export_html.go
  2500  internal/kg/export.go
  ...

🟡 Yellow (51 files 500-999 LOC)
  987   internal/server/handler.go
  ...

🟢 Green (449 files <500 LOC)
  [summary only]
```

### Plan Split

When you have a large file to split:

```
User: "tslsp_handler.go is over 2500 lines, help me split it"
Claude: [Uses go-filesize agent to analyze and plan split]
```

## Split Strategies

### Strategy 1: By Responsibility

Split file by distinct responsibilities.

**Before:**
```
handler.go (1500 lines)
  - HTTP handlers
  - Request validation
  - Response formatting
  - Middleware
```

**After:**
```
handler.go (400 lines)      - HTTP handlers
validation.go (300 lines)   - Request validation
response.go (200 lines)     - Response formatting
middleware.go (250 lines)   - Middleware
```

### Strategy 2: By Entity

Split by domain entity when file handles multiple.

**Before:**
```
store.go (2000 lines)
  - User CRUD
  - Order CRUD
  - Product CRUD
```

**After:**
```
store_user.go (600 lines)
store_order.go (700 lines)
store_product.go (500 lines)
store.go (200 lines)         - Shared helpers
```

### Strategy 3: By Complexity

Extract complex implementations to dedicated files.

**Before:**
```
parser.go (1200 lines)
  - Simple lexer (100 lines)
  - Complex expression parser (800 lines)
  - Simple formatter (100 lines)
```

**After:**
```
parser.go (200 lines)        - Public API
parser_expr.go (800 lines)   - Expression parsing
lexer.go (100 lines)         - Lexer
format.go (100 lines)        - Formatting
```

## Anti-Patterns

### DON'T
- Split files that are cohesive (all code relates)
- Create files under 50 lines
- Split by arbitrary line count
- Break logical groupings

### DO
- Keep related code together
- Split on clear responsibility boundaries
- Maintain test file correspondence
- Document large files that shouldn't be split

## Quality Targets

| Metric | Target |
|--------|--------|
| Files in red zone | 0 |
| Largest file | <800 LOC |
| Average file | 200-400 LOC |
