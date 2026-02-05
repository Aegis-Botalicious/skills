---
name: claude-code
description: Use Claude Code CLI for planning, architecture, and design work. Triggers when needing structured planning (/plan mode), PRD refinement, architecture decisions, or interactive design sessions with AskUserQuestionTool. Use for THINK phase before handing off to execution agents like Goose.
---

# Claude Code CLI

Use `claude` for planning, design, and architecture. Hands off execution to Goose.

## Quick Reference

```bash
claude                          # Interactive REPL
claude "query"                  # REPL with initial prompt
claude -p "query"               # Print mode (non-interactive, exits)
claude -c                       # Continue most recent conversation
claude -r "session"             # Resume by ID or name
```

## Permission Modes

```bash
claude --permission-mode plan      # Planning only, no file changes
claude --permission-mode default   # Normal prompting
claude --permission-mode delegate  # Delegate to subagents
```

In interactive mode, use `Shift+Tab` or `Alt+M` to cycle modes.

## Key Interactive Commands

| Command | Purpose |
|---------|---------|
| `/plan` | Enter plan mode directly |
| `/compact [focus]` | Compress context, optional focus instructions |
| `/context` | Visualize context usage as colored grid |
| `/cost` | Show token usage statistics |
| `/memory` | Edit CLAUDE.md memory files |
| `/init` | Initialize project with CLAUDE.md |
| `/model` | Switch model mid-session |
| `/rewind` | Restore conversation to previous point |
| `/clear` | Clear conversation history |
| `/resume [session]` | Resume by ID/name or open picker |
| `/export [file]` | Export conversation to file/clipboard |

## Quick Input Modes

| Prefix | Action |
|--------|--------|
| `!` | Run bash command directly (e.g., `! npm test`) |
| `@` | File path autocomplete (e.g., `@src/main.ts`) |
| `/` | Commands and skills |

## Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| `Ctrl+B` | Background running task |
| `Ctrl+L` | Clear terminal (keeps history) |
| `Ctrl+O` | Toggle verbose output |
| `Ctrl+R` | Reverse search history |
| `Shift+Tab` | Cycle permission modes |
| `Esc Esc` | Rewind conversation/code |
| `Option+P` | Switch model |

## Print Mode (Automation)

```bash
# One-shot query
claude -p "explain this function"

# Piped input
cat logs.txt | claude -p "analyze errors"

# JSON output for scripting
claude -p "query" --output-format json

# Streaming JSON
claude -p "query" --output-format stream-json

# With limits
claude -p "query" --max-turns 5 --max-budget-usd 1.00

# Custom system prompt (append to default)
claude --append-system-prompt "Always use TypeScript" "query"

# Replace system prompt entirely
claude --system-prompt "You are a Python expert" "query"
```

## Subagents

Define inline subagents for specialized tasks:

```bash
claude --agents '{
  "reviewer": {
    "description": "Code reviewer for quality checks",
    "prompt": "You are a senior code reviewer. Focus on security and best practices.",
    "tools": ["Read", "Grep", "Glob"],
    "model": "sonnet"
  }
}'
```

## MCP Configuration

```bash
claude mcp                           # Manage MCP servers
claude --mcp-config ./mcp.json       # Load MCP from file
```

## Session Management

```bash
claude -c                            # Continue last in current dir
claude -r "auth-refactor"            # Resume specific session
claude --resume abc123 --fork-session  # Fork existing session
claude --from-pr 123                 # Resume session linked to PR
```

## Planning Workflow

### 1. Start Plan Mode

```bash
cd ~/project
claude --permission-mode plan
```

### 2. Refine Requirements

```
/plan

Review this PRD and ask clarifying questions:
[paste PRD]
```

Claude uses AskUserQuestionTool to refine requirements interactively.

### 3. Create Architecture

```
Create architecture document with:
- Component breakdown
- Data flow diagrams
- API contracts
- File structure
```

### 4. Generate Implementation Plan

```
Create step-by-step implementation plan for a coding agent.
Number each task with clear acceptance criteria.
Save to docs/IMPLEMENTATION_PLAN.md
```

### 5. Handoff to Goose

```bash
# Goose executes the plan
goose run -i docs/IMPLEMENTATION_PLAN.md
```

## Background Execution

```bash
# Start with PTY for interactive
exec pty:true workdir:~/project background:true command:"claude --permission-mode plan"

# Monitor
process action:log sessionId:XXX

# Send input
process action:submit sessionId:XXX data:"Continue with step 2"
```

## Tips

- Use `/compact` regularly to manage context
- `! git status` to run commands without Claude processing
- `@src/` to quickly reference files
- `/cost` to monitor token usage
- `Esc Esc` to undo if Claude goes wrong direction
- Export plans as markdown for Goose handoff
