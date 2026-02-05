---
name: claude-code
description: Use Claude Code CLI for planning, architecture, and design work. Triggers when needing structured planning (/plan mode), PRD refinement, architecture decisions, or interactive design sessions with AskUserQuestionTool. Use for THINK phase before handing off to execution agents like Goose.
---

# Claude Code CLI

Use `claude` for planning, design, and architecture work. Hands off execution to Goose.

## Modes

| Mode | Flag | Use Case |
|------|------|----------|
| **Interactive** | `claude` | Full session with /plan, /compact |
| **Print** | `claude -p "prompt"` | One-shot, exits after response |
| **Plan** | `--permission-mode plan` | Planning mode, no execution |

## Key Features

### Plan Mode

For architecture and design without execution:

```bash
claude --permission-mode plan "Design the auth module for HabitYield"
```

In interactive mode, use `/plan` to switch:
```
/plan on    # Enable plan mode
/plan off   # Disable plan mode
```

### Continue Session

```bash
claude -c              # Continue last session in current dir
claude -r              # Resume by session picker
claude -r "search"     # Resume with search term
```

### Model Selection

```bash
claude --model opus "Complex architecture task"
claude --model sonnet "Standard task"
```

### Output Formats

```bash
# For piping/scripting
claude -p "prompt" --output-format json
claude -p "prompt" --output-format stream-json
```

## Workflow: Planning with Claude Code

### 1. Start Planning Session

```bash
cd ~/project
claude --permission-mode plan
```

### 2. Refine PRD/Design

Inside session:
```
/plan on

Review this PRD and ask clarifying questions:
[paste PRD]
```

Claude will use AskUserQuestionTool to refine requirements.

### 3. Create Architecture Doc

```
Create a detailed architecture document for this feature.
Include:
- Component breakdown
- Data flow
- API contracts
- File structure
```

### 4. Generate Implementation Plan

```
Create a step-by-step implementation plan that can be handed to a coding agent.
Format as numbered tasks with clear acceptance criteria.
```

### 5. Export for Goose

Save the plan to a file for Goose to execute:
```
Save this implementation plan to docs/IMPLEMENTATION_PLAN.md
```

## Integration with Goose

### Handoff Pattern

1. **Claude Code** creates:
   - `docs/ARCHITECTURE.md`
   - `docs/IMPLEMENTATION_PLAN.md`
   - `docs/API_SPEC.md`

2. **Goose** executes:
   ```bash
   goose session --name "feature-x" 
   > Execute the plan in docs/IMPLEMENTATION_PLAN.md
   ```

## Running in Background

For long planning sessions:

```bash
# Start with PTY
exec pty:true workdir:~/project background:true command:"claude --permission-mode plan"

# Monitor
process action:log sessionId:XXX

# Send input
process action:submit sessionId:XXX data:"your follow-up question"
```

## Common Commands

| Command | Purpose |
|---------|---------|
| `/plan on` | Enable plan mode |
| `/plan off` | Disable plan mode |
| `/compact` | Compress context |
| `/clear` | Clear conversation |
| `/help` | Show all commands |

## Tips

- Use `--permission-mode plan` for pure design work
- Export plans as markdown for Goose handoff
- Use `-c` to continue refining across sessions
- Pair with Goose for THINK → DO workflow
