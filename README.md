# Plan2Code

A Claude Code plugin that lets Claude handle the planning and code review, while delegating the actual code generation to [Codex CLI](https://github.com/openai/codex) via MCP. This saves Claude Code tokens on code generation — the most token-heavy part — while still getting Claude's architectural thinking and quality review.

## How It Works

```
You → /plan2code:plan2code <task>
        ↓
  Step 1: Claude plans the implementation (2-5 bullet points)
        ↓
  Step 2: Codex writes the code via MCP (consumes OpenAI tokens, not Claude)
        ↓
  Step 3: Claude verifies actual changes and reviews code quality
        ↓
  Step 3d: Issues found? → codex-reply to fix → re-verify
        ↓
  Done: Report files created/modified + summary to user
```

- **Claude** handles: planning, architecture, code review, change verification
- **Codex** handles: code generation, file creation, refactoring
- **Result**: Lower Claude token usage without sacrificing code quality

## Detailed Workflow

### Step 1: Plan
Claude analyzes the task and outlines implementation in 2-5 bullet points: files to create/modify, key logic, and entry-point files for Codex to read.

### Step 2: Delegate to Codex
Claude sends a concise English prompt to Codex via MCP — describing WHAT to do, not HOW. File paths are referenced, not pasted. Codex has full workspace access and figures out implementation details on its own.

### Step 3: Review
After Codex completes:
- **Verify changes**: `git diff` + `git status` (in git repos) or `find`/`ls` (non-git directories) to confirm exactly what was changed
- **Cross-check**: Compare Codex's reported changes against actual changes on disk — flag unexpected modifications
- **Code review**: Read key files, check for errors, edge cases, and security issues
- **Auto-fix**: If issues found, use `codex-reply` (same session) to request corrections, then re-verify

## Prerequisites

- [Claude Code](https://claude.com/claude-code) installed
- [Codex CLI](https://github.com/openai/codex) installed (`npm install -g @openai/codex`)
- Codex configured as MCP server in Claude Code:
  ```bash
  claude mcp add codex -- codex mcp-server
  ```

## Installation

```bash
claude plugin marketplace add memory009/Plan2Code
claude plugin install plan2code@cheeson-plugins
```

## Usage

In Claude Code (CLI or VSCode plugin):

```
/plan2code:plan2code create a REST API for user authentication in src/auth.ts
```

```
/plan2code:plan2code 在 ~/Documents/pomodoro 目录创建一个番茄钟应用，Python实现
```

### When to use

- Implementation plan is clear and needs coding execution
- Batch refactoring, code generation, or multi-file changes
- Writing or updating tests
- Bug fixes where the root cause is identified

### When NOT to use

- Architecture design and high-level strategy (Claude is better)
- Ambiguous or exploratory problems
- Small edits of a few lines (not worth the overhead)

## Token Cost Comparison

| Step | Who | Token Source | Cost |
|------|-----|-------------|------|
| Planning | Claude | Anthropic | Small |
| Code generation | Codex | **OpenAI** | Main cost |
| Change verification | Claude | Anthropic | Small |
| Code review | Claude | Anthropic | Small |
| Auto-fix (if needed) | Codex | **OpenAI** | Occasional |

The code generation step — typically the largest token consumer — is offloaded to Codex (OpenAI tokens), while Claude only handles the lightweight planning, verification, and review steps.

## Project Structure

```
.claude-plugin/
  marketplace.json
plugins/
  plan2code/
    .claude-plugin/
      plugin.json
    skills/
      plan2code/
        SKILL.md          # Skill definition and workflow
    README.md
```

## License

MIT
