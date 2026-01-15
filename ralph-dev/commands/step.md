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
4. **Run tests**: Verify the implementation passes automated checks:
   - Flutter: Run `flutter analyze`
   - React/JS: Run linter (eslint, etc.)
   - Other: Run appropriate test command
5. **Update PRD**: Set `passes: true` for this requirement ONLY if tests pass
6. **Log progress**: Append a summary to the progress file

## Important Rules

- ONLY work on ONE requirement - do not start additional ones
- A requirement `passes: true` means BOTH implemented AND tests pass
- If tests fail, keep `passes: false` and note the issue in progress
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
