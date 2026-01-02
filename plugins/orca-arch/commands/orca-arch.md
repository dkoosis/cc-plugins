---
description: Start a structured architecture discussion about Orca. Use when considering new features, refactors, or design changes. Prevents premature implementation.
---

# Orca Architecture Discussion

You are starting a structured architecture conversation. **Do not write any code or make any changes yet.**

## Phase 1: Problem Definition (REQUIRED)

Before discussing solutions, establish:

1. **What problem are we solving?** 
   - State the problem in one sentence
   - Ask: "Is this the right framing of the problem?"

2. **What's driving this?**
   - User pain? Technical debt? New capability? 
   - What happens if we do nothing?

3. **What constraints exist?**
   - What must not change?
   - What external dependencies matter?
   - What have we tried before that didn't work?

**STOP HERE** and confirm alignment before proceeding.

## Phase 2: Context Discovery

Use the code-explorer agent to understand:
- What currently exists in this area?
- **Why does it exist this way?** (Ask explicitly if unclear)
- What depends on it?
- What past decisions shaped this?

Search for relevant Orca nuggets about architectural decisions.

**STOP HERE** and summarize your understanding. Ask: "What am I missing or getting wrong?"

## Phase 3: Options with Tradeoffs

Present **at least 3 approaches** with:
- What it solves well
- What it makes harder
- What assumptions it requires
- Rough effort estimate

Ask: "Which tradeoffs matter most to you?"

## Phase 4: Stress Test

For the preferred approach:
- "What are three ways this could fail to meet your needs?"
- "What assumptions are we making that might be wrong?"
- "What's the smallest thing we could build to validate this direction?"

## Phase 5: Decision Capture

Before implementation, create a nugget capturing:
- The decision made
- Why (the reasoning, not just the outcome)
- What alternatives were considered
- What constraints shaped the choice

This becomes the authoritative record, not implementation artifacts.

---

## Anti-patterns to Avoid

❌ **Forensic spirals**: Don't explore the codebase for 50 steps. Bound exploration.
❌ **Premature proposals**: Don't suggest solutions before understanding constraints.
❌ **Confident wrongness**: Don't assume you understand why something exists.
❌ **Implementation creep**: Don't start coding until Phase 5 is complete.

---

$ARGUMENTS will contain the topic. Begin with Phase 1.
