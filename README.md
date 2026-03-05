# Plan2Code

A Claude Code plugin that lets Claude handle the planning and code review, while delegating the actual code generation to [Codex CLI](https://github.com/openai/codex) via MCP. This saves Claude Code tokens on code generation — the most token-heavy part — while still getting Claude's architectural thinking and quality review.

## How It Works

```
You → /plan2code:plan2code <task>
        ↓
  Claude plans the implementation (2-5 bullet points)
        ↓
  Codex writes the code via MCP (consumes OpenAI tokens, not Claude)
        ↓
  Claude reviews the result and reports back
```

- **Claude** handles: planning, architecture, code review
- **Codex** handles: code generation, file creation, refactoring
- **Result**: Lower Claude token usage without sacrificing code quality

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
/plan2code:plan2code 在 ~/Documents/myapp 目录创建一个番茄钟应用，Python实现
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

| Step | Who | Token Source |
|------|-----|-------------|
| Planning | Claude | Anthropic |
| Code generation | Codex | **OpenAI** |
| Review | Claude | Anthropic (small) |

The code generation step — typically the largest token consumer — is offloaded to Codex (OpenAI tokens), while Claude only handles the lightweight planning and review steps.

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
