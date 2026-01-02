# Orca Architecture Plugin

A Claude Code plugin for structured architecture discussions about the Orca project. Designed to prevent common failure modes when working with Claude on architectural decisions.

## Problem This Solves

From actual experience working on Orca:
- **Premature implementation**: Claude dives into code before understanding constraints
- **Forensic spirals**: 50-step explorations that go nowhere  
- **Confident wrongness**: Proposing to remove things without understanding why they exist
- **Stale knowledge**: Nuggets capturing state rather than decisions become misinformation

## Components

### Command: `/orca-arch`

Initiates a structured architecture discussion with 5 phases:
1. Problem Definition - establish what we're solving and constraints
2. Context Discovery - understand what exists and why
3. Options with Tradeoffs - present alternatives
4. Stress Test - argue against the preferred approach
5. Decision Capture - create a nugget recording the decision

### Agent: `orca-explorer`

Bounded codebase exploration that:
- Limits to 10 tool calls per exploration round
- Searches nuggets before diving into code
- Surfaces unknowns explicitly rather than guessing
- Focuses on purpose and constraints, not implementation details

### Skill: `orca-architecture`

Auto-activates when working on Orca to provide:
- Current architecture context (global DBs, not per-project)
- Known historical migrations and stale information
- Principles for good architectural work
- Common pitfalls from past sessions

## Installation

Add this plugin to your personal marketplace, then install:

```bash
/plugin marketplace add <your-marketplace>
/plugin install orca-arch
```

Or install directly from local path during development:

```bash
/plugin install /path/to/orca-arch-plugin
```

## Usage

```
/orca-arch Add semantic search to the knowledge graph
```

Claude will guide you through the structured phases before any implementation begins.
