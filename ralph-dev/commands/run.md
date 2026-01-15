---
description: Run multiple PRD steps sequentially with fresh context isolation
argument-hint: [count] [--prd <path>] [--progress <path>]
allowed-tools: Bash, Read
---

Orchestrate multiple PRD development iterations, each in a fresh Claude context with full visibility.

## Configuration

Parse arguments:
- Iteration count: First positional argument or default to 10
- PRD file: Use `--prd <path>` or default to `.ralph/PRD.json`
- Progress file: Use `--progress <path>` or default to `.ralph/progress.txt`

Arguments provided: $ARGUMENTS

## Execution Process

**CRITICAL: Run iterations SEQUENTIALLY in FOREGROUND for full visibility.**

For each iteration (1 to count):

### Step 1: Announce Iteration
Output: `Starting iteration X of Y...`

### Step 2: Read Current PRD State
Use the Read tool to check current PRD.json state. Count requirements where `passes: true` vs `passes: false`.

### Step 3: Run Fresh Claude Process
Execute the step command in a fresh Claude process:

```bash
claude --permission-mode acceptEdits -p "/ralph-dev:step --prd <prd-path> --progress <progress-path>"
```

**CRITICAL - MUST RUN IN FOREGROUND:**
When calling the Bash tool, you MUST:
- NOT set `run_in_background: true`
- Set `timeout: 600000` (10 minutes) to allow complex implementations
- The Bash tool will stream output to the user in real-time when running in foreground

**Why use /ralph-dev:step:**
- All the implementation logic is already there
- Consistent behavior between manual and automated runs
- Easier to maintain one command definition
- Plugin access is inherited from user's installed plugins

### Step 4: Check Output
After the command completes:
- If output contains `<promise>COMPLETE</promise>`: Stop and report "PRD COMPLETE"
- If command failed/timed out: Report error and ask user whether to continue
- Otherwise: Continue to next iteration

### Step 5: Report Iteration Result
After each iteration, briefly summarize:
- Which feature was worked on (read from progress.txt)
- Success/failure status
- Remaining iterations

## Error Handling

- If a Claude process fails, report the error clearly
- Ask user: "Iteration X failed. Continue with remaining iterations? (Y/N)"
- If timeout occurs, kill the process and report

## Final Summary

After all iterations (or early completion):
- Total iterations run
- Features completed in this session
- Whether PRD is fully complete
- Suggest next steps

## Important Notes

- Run FOREGROUND, not background - user needs to see progress
- SEQUENTIAL execution - wait for each to complete before next
- Each iteration gets FRESH context (new Claude process)
- Use `--permission-mode acceptEdits` for auto-accept
- Stop immediately on `<promise>COMPLETE</promise>`
