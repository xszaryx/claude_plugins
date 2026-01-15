---
description: Create a JSON PRD from a markdown PRD description
argument-hint: <markdown-prd-path>
allowed-tools: Read, Write, Edit, Glob, Grep, Bash, Task
---

Convert a markdown PRD into a structured JSON PRD with atomic requirements for ralph-dev workflow.

## Configuration

Parse the markdown PRD path from arguments:
- Markdown PRD: First positional argument (required)
- PRD output: `.ralph/PRD.json`
- Progress output: `.ralph/progress.txt`

Arguments provided: $ARGUMENTS

## Execution Steps

### 1. Validate Input
Confirm the markdown PRD path was provided. If not, ask the user for the path.

### 2. Archive Existing PRD
If `.ralph/PRD.json` exists:

1. Spawn the `archive-namer` agent using Task tool with `subagent_type: ralph-dev:archive-namer`:
   ```
   Read .ralph/PRD.json and output ONLY a short, filesystem-safe folder name describing the PRD content (lowercase, underscores, max 40 chars).
   ```

2. Create archive folder with format: `.ralph/archive/YYYY.MM.DD_<name>/`

3. Move files to archive:
   ```bash
   mkdir -p ".ralph/archive/<folder-name>"
   mv .ralph/PRD.json ".ralph/archive/<folder-name>/"
   mv .ralph/progress.txt ".ralph/archive/<folder-name>/" 2>/dev/null || true
   ```

### 3. Read and Analyze

1. **Read the markdown PRD** at the specified path
2. **Analyze the codebase** using Glob and Grep to understand:
   - Project structure and tech stack
   - Existing patterns and conventions
   - Files that will be modified
   - Test structure

### 4. Generate JSON PRD

Create `.ralph/PRD.json` as an **array of requirements**:

```json
[
  {
    "category": "functional",
    "description": "Brief feature statement describing what should happen",
    "steps": [
      "Observable outcome that proves it works",
      "Another observable behavior",
      "Third verification point"
    ],
    "passes": false
  }
]
```

**Requirement Format:**
- `category`: `"functional"` or `"non-functional"`
- `description`: Brief but complete feature statement (not artificially short)
- `steps`: 2-4 observable outcomes/behaviors that prove it works (user story style)
- `passes`: `false` initially, set to `true` when implemented AND tests pass

**Requirement Guidelines:**
- Each requirement = ONE atomic, testable behavior
- NOT big tasks with sub-tasks
- Each should be independently verifiable
- Steps describe WHAT should be true, not HOW to verify
- Group related behaviors logically (e.g., all filter chip behaviors together)

**Example Requirements:**
```json
{
  "category": "functional",
  "description": "Combined leaderboard is the default view when opening Leaderboards screen",
  "steps": [
    "'All Games' filter chip is selected by default",
    "Combined scores from both games are displayed",
    "Trophy icon appears next to each score"
  ],
  "passes": false
},
{
  "category": "functional",
  "description": "Tapping a filter chip switches the leaderboard view",
  "steps": [
    "Tapping 'Quote Falls' shows only Quote Falls scores",
    "Tapping 'Quote Breaker' shows only Quote Breaker scores",
    "Tapping 'All Games' shows combined scores"
  ],
  "passes": false
},
{
  "category": "non-functional",
  "description": "Combined leaderboard loads within 3 seconds",
  "steps": [
    "Initial load completes in under 3 seconds",
    "Loading indicator shows during fetch",
    "No visible lag when switching filters"
  ],
  "passes": false
}
```

**What `passes` means:**
- `false`: Not yet implemented, or implementation doesn't pass tests
- `true`: Implemented AND passes automated checks (flutter analyze, unit tests, linter)

### 5. Create Progress File

Create `.ralph/progress.txt`:

```
# Progress Log for <project name>
# Created: <ISO timestamp>
# Source PRD: <markdown PRD path>

```

### 6. Report Summary

Output:
- Number of requirements generated (functional vs non-functional)
- Suggested next step: `/ralph-dev:step` or `/ralph-dev:run`

## Example Usage

```
/ralph-dev:create-prd docs/feature_spec.md
/ralph-dev:create-prd specs/new-auth-system.md
/ralph-dev:create-prd requirements/leaderboard-redesign.md
```
