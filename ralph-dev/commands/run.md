---
description: Run multiple PRD steps sequentially using isolated agents with progress monitoring
argument-hint: [count] [--prd <path>] [--progress <path>]
allowed-tools: Task, Read
---

Orchestrate multiple PRD development iterations using the step-executor agent with progress monitoring.

## Configuration

Parse arguments:
- Iteration count: First positional argument or default to 10
- PRD file: Use `--prd <path>` or default to `.ralph/PRD.json`
- Progress file: Use `--progress <path>` or default to `.ralph/progress.txt`

Arguments provided: $ARGUMENTS

## Before Starting

1. Read the PRD.json to understand current state
2. Count requirements where `passes: true` vs `passes: false`
3. Report: "Starting X iterations. PRD has Y/Z requirements passing."

## Execution Loop

For each iteration (1 to count):

### Step 1: Announce
Output: `Starting iteration X of Y...`

### Step 2: Spawn Step-Executor Agent

Use the Task tool to spawn the step-executor agent in **foreground** with auto-approve:

```
Task tool parameters:
- subagent_type: "ralph-dev:step-executor"
- prompt: "Execute one PRD step. PRD path: <prd-path>, Progress path: <progress-path>"
- mode: "bypassPermissions"
- description: "PRD step X"
```

**Note**: No `run_in_background` - the agent runs in foreground so output is visible in real-time. The `mode: "bypassPermissions"` auto-approves edits without manual intervention.

Wait for the agent to complete. The output will stream to the user as it works.

### Step 3: Check Result

After the agent completes:
- If output contains `<promise>COMPLETE</promise>`: Stop iterations and report "PRD COMPLETE"
- If agent failed: Report error and ask user whether to continue
- Otherwise: Continue to next iteration

### Step 4: Report Iteration Result

After each iteration:
- Read progress.txt to see what was done
- Briefly summarize: which requirement, pass/fail, remaining iterations

## Error Handling

- If an agent fails, report the error clearly
- Ask user: "Iteration X failed. Continue with remaining iterations? (Y/N)"
- If timeout occurs, report and ask user

## Final Summary

After all iterations (or early completion):
- Total iterations run
- Requirements completed in this session
- Whether PRD is fully complete
- Suggest next steps if incomplete

## Key Benefits of This Approach

- **Fresh context**: Each agent invocation gets isolated context
- **Real-time visibility**: Foreground execution shows output as it happens
- **Opus model**: Step-executor uses Opus for high-quality implementations
- **Auto-approve**: bypassPermissions mode means no manual approval needed
