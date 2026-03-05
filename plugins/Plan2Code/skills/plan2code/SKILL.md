---
name: plan2code
description: Plan a task with Claude, delegate code implementation to Codex via MCP, then review the result. Use when the user wants Claude to plan and Codex to write code.
---

# Plan2Code

The user wants you to plan and delegate code implementation to Codex.

Task: $ARGUMENTS

## Workflow

### Step 1: Plan
Briefly outline the implementation in 2-5 bullet points. Focus on:
- What files to create or modify
- Key logic and structure
- Dependencies or considerations

### Step 2: Delegate to Codex
Call the `codex` MCP tool with:
- `prompt`: A clear, specific implementation instruction based on your plan
- `cwd`: The appropriate working directory
- `sandbox`: "workspace-write"

### Step 3: Review
After Codex returns the result:
- Verify the code matches your plan
- Check for obvious errors or missing pieces
- Report the result to the user with a brief summary

## Rules
- Keep the plan concise, do not over-explain
- Write the Codex prompt in English for best results
- If the task is too large, break it into multiple Codex calls
- If Codex's output has issues, use `codex-reply` to ask for corrections
