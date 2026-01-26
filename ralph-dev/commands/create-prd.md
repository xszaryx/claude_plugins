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
- `steps`: 3-6 observable outcomes/behaviors that prove it works (user story style)
- `passes`: `false` initially, set to `true` when implemented AND tests pass

**Requirement Guidelines:**
- Each requirement = ONE logical feature/section that groups related behaviors
- Aim for 10-25 requirements for a typical PRD (not 50-100)
- Group by: functional area, component, user flow, or technical layer
- Steps contain the related atomic behaviors/outcomes within that section (3-6 steps per requirement)
- Steps describe WHAT should be true, not HOW to verify
- Each requirement should be independently verifiable as a unit

**Example Requirements:**
```json
{
  "category": "functional",
  "description": "Filter chip functionality allows switching between leaderboard views",
  "steps": [
    "'All Games' filter chip is selected by default on screen open",
    "Tapping 'Quote Falls' shows only Quote Falls scores",
    "Tapping 'Quote Breaker' shows only Quote Breaker scores",
    "Tapping 'All Games' shows combined scores with trophy icons",
    "Selected chip has visual distinction from unselected chips"
  ],
  "passes": false
},
{
  "category": "functional",
  "description": "Design tokens are fully defined and accessible",
  "steps": [
    "Spacing scale exists with values xs(4), sm(8), md(16), lg(24), xl(32), xxl(48), xxxl(64)",
    "Border radius scale exists with values sm(4), md(8), lg(16), full(999)",
    "Animation duration scale exists with values micro(100ms), small(200ms), medium(300ms), large(400ms)",
    "All token values are accessible via respective classes/constants",
    "Tokens can be used in widget padding, margins, and decorations"
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
