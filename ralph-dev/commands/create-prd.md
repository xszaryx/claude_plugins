---
description: Create a JSON PRD from a markdown PRD description
argument-hint: <markdown-prd-path>
allowed-tools: Read, Write, Edit, Glob, Grep, Bash, Task
---

Convert a markdown PRD into a structured JSON PRD with atomic tasks for ralph-dev workflow.

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

Create `.ralph/PRD.json` with structure:

```json
{
  "name": "<project/feature name>",
  "version": "1.0.0",
  "description": "<brief description>",
  "source": "<markdown PRD path>",
  "created": "<ISO timestamp>",
  "features": [
    {
      "id": "feature-1",
      "name": "<short name>",
      "priority": 1,
      "status": "pending",
      "description": "<what to accomplish>",
      "acceptance_criteria": ["<criterion>"]
    }
  ]
}
```

**Task Guidelines:**
- ~30 minutes per task (small, atomic)
- One clear objective per task
- Independently testable
- Priority 1-10 (1 = highest, do first)
- Order by logical dependency

**Task Categories:**
1. Setup/scaffolding tasks
2. Core implementation tasks
3. Integration tasks
4. Testing tasks
5. Polish/edge case tasks

### 5. Create Progress File

Create `.ralph/progress.txt`:

```
# Progress Log for <project name>
# Created: <ISO timestamp>
# Source PRD: <markdown PRD path>

```

### 6. Report Summary

Output:
- Number of tasks generated
- Task breakdown by category
- Suggested next step: `/ralph-dev:step` or `/ralph-dev:run`

## Example Usage

```
/ralph-dev:create-prd docs/feature_spec.md
/ralph-dev:create-prd specs/new-auth-system.md
/ralph-dev:create-prd requirements/leaderboard-redesign.md
```
