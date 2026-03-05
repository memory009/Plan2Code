---
description: Claude plans, Codex codes via MCP, Claude reviews. Use for coding tasks where the plan is clear.
argument-hint: <task description>
allowed-tools: [Read, Glob, Grep, Bash, mcp__codex__codex, mcp__codex__codex-reply]
---

# Plan2Code

Task: $ARGUMENTS

## When to use this command

Use `/plan2code` when:
- The implementation plan is clear and needs coding execution
- Batch refactoring, code generation, or multi-file changes
- Writing or updating tests based on existing code
- Bug fixes where the root cause is identified

Do NOT use — Claude handles these better directly:
- Architecture design and high-level strategy
- Ambiguous or exploratory problems
- Small edits (a few lines) — not worth the overhead

## Workflow

### Step 1: Plan

Analyze the task and outline the implementation in 2-5 bullet points:
- What files to create or modify
- Key logic, patterns, and constraints
- Identify 2-6 key entry-point files Codex should read

### Step 2: Delegate to Codex

Call the `codex` MCP tool with these parameters:
- `prompt`: Describe WHAT to do and key constraints. Do NOT describe step-by-step HOW — Codex is an autonomous agent with full workspace access, it reads files and figures out implementation details on its own. Keep under ~500 words. Write in English.
- `cwd`: The project working directory
- `sandbox`: "workspace-write"

**Prompt guidelines:**
- Tell Codex which files to read (paths), do NOT paste file contents into the prompt
- State the goal and constraints, not micro-instructions
- If multiple files are involved, list them so Codex knows where to look

### Step 3: Review

After Codex returns:
- Verify the code matches the plan
- Check for errors, missing edge cases, or security issues
- If issues found, use `codex-reply` to request corrections (include the threadId)
- Report a brief summary to the user

## Rules

- One Codex call per focused task. If the task is large, break into multiple calls.
- For follow-up corrections, always use `codex-reply` with the same threadId.
- Never re-send the same prompt. If Codex succeeded, move on.
