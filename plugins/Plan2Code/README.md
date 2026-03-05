# Plan2Code

Claude plans, Codex codes, Claude reviews.

## Prerequisites

- Codex CLI installed (`codex`)
- Codex configured as MCP server: `claude mcp add codex -- codex mcp-server`

## Usage

```
/plan2code create a REST API for user authentication in src/auth.ts
```

## Workflow

1. **Plan** — Claude analyzes the task and outlines implementation steps
2. **Code** — Codex writes the code via MCP
3. **Review** — Claude verifies the output and reports back
