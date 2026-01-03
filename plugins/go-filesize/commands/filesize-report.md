---
name: filesize-report
description: Generate a report of Go file sizes with tier classification (green/yellow/red) and split recommendations.
model: haiku
tools:
  - Bash
  - Glob
  - Read
---

# File Size Report

Generate a report of Go file sizes organized by tier.

## Usage

```
/filesize-report [path]
```

**Arguments:**
- `path` - Directory to analyze (default: `./...`)

## Process

1. Find all .go files (excluding *_test.go, vendor/):
```bash
find $ARGUMENTS -name "*.go" ! -name "*_test.go" ! -path "*/vendor/*" -exec wc -l {} \; | sort -rn
```

2. Categorize by tier:
   - 🔴 Red: ≥1000 lines
   - 🟡 Yellow: 500-999 lines
   - 🟢 Green: <500 lines

3. For red files, identify potential split points:
   - Count exported vs unexported symbols
   - Identify distinct functionality groups
   - Note if file is a single large type (may be appropriate)

## Output Format

```
## File Size Report

Generated: [timestamp]
Path: [analyzed path]

### Summary

| Tier | Files | % of Total |
|------|-------|------------|
| 🔴 Red (≥1000) | 16 | 3% |
| 🟡 Yellow (500-999) | 51 | 10% |
| 🟢 Green (<500) | 449 | 87% |

### 🔴 Red Zone (16 files)

| Lines | File | Recommendation |
|-------|------|----------------|
| 2563 | internal/lsp/tslsp_handler.go | Split by handler type |
| 2526 | internal/kg/export_html.go | Extract template logic |
| 2500 | internal/kg/export.go | Split by export format |
| 1843 | internal/jobs/scheduled.go | Split by job type |
| 1695 | internal/metrics/tools.go | Split by metric category |
...

### 🟡 Yellow Zone (51 files)

[Top 10 by size, summarized]

### Trends

| Period | Red | Yellow | Green |
|--------|-----|--------|-------|
| Current | 16 | 51 | 449 |
| Last week | 15 | 43 | 416 |
| Change | +1 | +8 | +33 |

### Recommendations

1. **Immediate:** tslsp_handler.go (2563 LOC) - largest file
2. **High Priority:** export_html.go, export.go - related, could share extraction
3. **Monitor:** 8 yellow files approaching 1000 LOC threshold
```

## Notes

- Test files are excluded (duplication with tables is normal)
- Generated files should be noted but may be acceptable
- Single-type files (e.g., large struct with methods) may be appropriate to keep together
