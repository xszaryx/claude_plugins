---
description: Execute ONE PRD feature task in current session
argument-hint: [--prd <path>] [--progress <path>]
allowed-tools: Read, Write, Edit, Glob, Grep, Bash, Task, WebFetch, WebSearch
---

Execute a single PRD-driven development step.

## Configuration

Parse arguments for custom paths:
- PRD file: Use `--prd <path>` or default to `.ralph/PRD.json`
- Progress file: Use `--progress <path>` or default to `.ralph/progress.txt`

Arguments provided: $ARGUMENTS

## Execution Steps

1. **Read PRD**: Load the PRD JSON file and parse the features/tasks
2. **Find highest priority**: Identify the highest-priority incomplete feature (status: "pending" or "in_progress")
3. **Implement the feature**: Work ONLY on that single feature
4. **Run tests**: Verify the implementation by running tests
5. **Update PRD**: Mark the feature as "completed" in the PRD JSON file
6. **Log progress**: Append a summary of what was done to the progress file

## Important Rules

- ONLY work on ONE feature - do not start additional features
- If all features are complete, output `<promise>COMPLETE</promise>` at the end of your response
- Always update the PRD.json with completion status
- Always append progress to progress.txt with timestamp and summary
- Run tests to verify the implementation works

## Progress Entry Format

When appending to progress.txt, use this format:
```
---
Timestamp: [ISO timestamp]
Feature: [feature name/ID from PRD]
Status: Completed
Summary: [brief description of what was done]
---
```
