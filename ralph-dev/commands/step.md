---
description: Execute ONE PRD requirement in current session
argument-hint: [--prd <path>] [--progress <path>]
allowed-tools: Read, Write, Edit, Glob, Grep, Bash, Task, WebFetch, WebSearch
---

Execute a single PRD-driven development step.

## Configuration

Parse arguments for custom paths:
- PRD file: Use `--prd <path>` or default to `.ralph/PRD.json`
- Progress file: Use `--progress <path>` or default to `.ralph/progress.txt`

Arguments provided: $ARGUMENTS

## PRD Format

The PRD is an array of requirements:
```json
[
  {
    "category": "functional",
    "description": "Feature statement",
    "steps": ["Observable outcome 1", "Observable outcome 2"],
    "passes": false
  }
]
```

## Execution Steps

1. **Read PRD**: Load the PRD JSON array
2. **Find next requirement**: Identify a requirement where `passes: false`. Choose the one YOU decide is highest priority - not necessarily the first in the list.
3. **Implement the requirement**: Work ONLY on that single requirement, ensuring all `steps` are satisfied
4. **Write minimal tests & verify**:
   - Write ONLY essential behavior tests (3-5 max per requirement)
   - Don't test simple UI, framework behavior, or implementation details
   - Run verification: `flutter analyze`, existing test suite
5. **Update PRD**: Set `passes: true` for this requirement ONLY if verification passes
6. **Log progress**: Append a summary to the progress file

## Important Rules

- ONLY work on ONE requirement - do not start additional ones
- Write MINIMAL tests only (3-5 max) - focus on behavior, not implementation details
- A requirement `passes: true` means implemented AND verification passes
- If verification fails, keep `passes: false` and note the issue in progress
- Don't over-test simple UI components - quality over quantity
- If ALL requirements have `passes: true`, output `<promise>COMPLETE</promise>` at the end
- Always update PRD.json with the new passes status
- Always append progress to progress.txt with timestamp and summary

## Progress Entry Format

When appending to progress.txt, use this format:
```
---
Timestamp: [ISO timestamp]
Requirement: [description from PRD]
Passes: [true/false]
Summary: [brief description of what was done]
---
```
