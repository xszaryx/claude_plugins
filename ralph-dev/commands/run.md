---
description: Run multiple PRD steps sequentially with fresh context isolation
argument-hint: [count] [--prd <path>] [--progress <path>]
allowed-tools: Bash
---

Orchestrate multiple PRD development iterations, each in a fresh Claude context.

## Configuration

Parse arguments:
- Iteration count: First positional argument or default to 10
- PRD file: Use `--prd <path>` or default to `.ralph/PRD.json`
- Progress file: Use `--progress <path>` or default to `.ralph/progress.txt`

Arguments provided: $ARGUMENTS

## Execution

For each iteration (up to the specified count):

1. Spawn a fresh Claude process with:
   ```bash
   claude --permission-mode acceptEdits -p "@<prd-path> @<progress-path> \
   1. Find the highest-priority feature to work on and work only on that feature. \
   2. Check that the tests pass. \
   3. Update the PRD (<prd-path>) with the work that was done. \
   4. Append your progress to the <progress-path> file. \
   ONLY WORK ON A SINGLE FEATURE \
   If, while implementing the feature, you notice the PRD is complete, output <promise>COMPLETE</promise>."
   ```

2. Capture the output

3. Check if output contains `<promise>COMPLETE</promise>`:
   - If yes: Stop iterations and report "PRD COMPLETE"
   - If no: Continue to next iteration

4. Report progress after each iteration

## Important Notes

- Each iteration runs in a FRESH Claude context (critical for context isolation)
- Use `--permission-mode acceptEdits` to auto-accept file edits
- Stop early if PRD is marked complete
- Sequential execution ensures each step sees previous progress
