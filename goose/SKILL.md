---
name: goose
description: Use Goose AI agent for code execution, building, and implementation. Triggers when executing implementation plans, coding tasks, file operations, testing, and multi-model development work. Use for DO phase after planning with Claude Code. Supports multi-model configuration (Opus, Codex, local LLM).
---

# Goose CLI

Use `goose` for execution, coding, and building. Receives plans from Claude Code.

## Quick Reference

```bash
goose session                       # Start interactive session
goose session -n my-project         # Named session (resumable)
goose session -n my-project -r      # Resume named session
goose run -t "instructions"         # Execute text directly
goose run -i instructions.md        # Execute from file
goose configure                     # Configure provider/model
```

## Core Commands

### Interactive Session

```bash
goose session                           # Basic session
goose session -n "feature-auth"         # Named session
goose session -r -n "feature-auth"      # Resume by name
goose session -r --session-id 20250205_1  # Resume by ID
goose session --fork -r                 # Fork most recent session
goose session --history -r              # Show history when resuming
```

### Run (Automation)

```bash
# Direct text
goose run -t "Create a REST API for todos"

# From instruction file
goose run -i instructions.md

# From stdin
echo "Fix the login bug" | goose run -i -

# Multi-line stdin
cat << EOF | goose run -i -
1. Run tests
2. Fix failures
3. Commit changes
EOF

# Stay interactive after initial task
goose run -i instructions.md -s

# Named run session (resumable)
goose run -n "daily-tasks" -t "Run morning checks"
goose run -n "daily-tasks" -r  # Resume
```

### Recipes

```bash
goose run --recipe recipe.yaml
goose run --recipe recipe.yaml --params key=value
```

## Provider & Model Selection

Configure once:
```bash
goose configure
```

Or per-command:
```bash
goose run --provider anthropic --model claude-sonnet-4-20250514 -t "task"
goose run --provider openai --model gpt-4o -t "task"
goose session --provider anthropic --model claude-opus-4-20250514
```

## Extensions

### Built-in Extensions

```bash
# List available
goose mcp list

# Use in session
goose session --with-builtin developer
goose session --with-builtin "developer,computercontroller"

# Use in run
goose run --with-builtin developer -t "Set up the project"
```

### Custom Extensions (MCP)

```bash
# Stdio extension
goose session --with-extension "npx -y @modelcontextprotocol/server-memory"

# With environment variables
goose session --with-extension "API_KEY=xyz custom-tool args"

# HTTP extension
goose session --with-streamable-http-extension "http://localhost:8080/mcp"

# Multiple extensions
goose session \
  --with-builtin developer \
  --with-extension "custom-tool" \
  --with-streamable-http-extension "http://localhost:8080/mcp"
```

## Output Formats (Automation)

```bash
# JSON output (for CI/CD)
goose run --output-format json -t "task"

# Streaming JSON (real-time)
goose run --output-format stream-json -t "task"

# No session file (one-off scripts)
goose run --no-session -t "automated task"

# Combined for CI
goose run --output-format json --no-session -t "run tests"
```

## Session Management

```bash
goose session list                      # List all sessions
goose session list --format json        # JSON output
goose session list --limit 10           # Last 10 only
goose session list -w ~/project         # Filter by directory

goose session remove                    # Interactive removal
goose session remove -n my-project      # Remove by name
goose session remove -r "test-.*"       # Remove by regex

goose session export -n my-project      # Export as markdown
goose session export -n my-project --format json -o backup.json

goose session diagnostics -n my-project # Generate debug bundle
```

## Debug Mode

```bash
goose session --debug                   # Verbose tool output
goose run --debug -t "task"             # Debug run
```

Shows complete tool responses, parameter values, and full paths.

## Behavior Controls

```bash
# Limit consecutive identical tool calls (prevent loops)
goose session --max-tool-repetitions 3

# Limit turns without user input
goose session --max-turns 25
```

## Docker Container

```bash
goose session --container my-container
goose run --container my-container -t "task"
```

## Executing Plans from Claude Code

### Pattern 1: Direct File Execution

```bash
# Claude Code created docs/IMPLEMENTATION_PLAN.md
goose run -i docs/IMPLEMENTATION_PLAN.md
```

### Pattern 2: Named Session for Complex Work

```bash
# Start
goose session -n "habityield-phase1"

# In session, reference the plan
> Execute the implementation plan in docs/IMPLEMENTATION_PLAN.md, step by step.
> Commit after each major milestone.

# Later, resume
goose session -n "habityield-phase1" -r
```

### Pattern 3: CI/CD Integration

```bash
goose run \
  --provider anthropic \
  --model claude-sonnet-4-20250514 \
  --output-format json \
  --no-session \
  -i ci-instructions.md
```

## Background Execution

```bash
# Start with PTY
exec pty:true workdir:~/project background:true command:"goose session -n overnight"

# Monitor
process action:log sessionId:XXX

# Send input
process action:submit sessionId:XXX data:"Continue to next step"

# Kill if needed
process action:kill sessionId:XXX
```

## Multi-Model Strategy

| Task | Provider | Model | Flag |
|------|----------|-------|------|
| Complex reasoning | anthropic | claude-opus-4 | `--provider anthropic --model claude-opus-4-20250514` |
| Code generation | openai | gpt-4o | `--provider openai --model gpt-4o` |
| Quick tasks | ollama | codellama | `--provider ollama --model codellama:latest` |
| General | anthropic | claude-sonnet-4 | `--provider anthropic --model claude-sonnet-4-20250514` |

## Tips

- Use `goose run -t` for one-off automation
- Use `goose session -n` for complex multi-step work
- Add `--debug` when troubleshooting
- Use `--output-format json` for CI/CD pipelines
- Use `--no-session` for stateless automation
- Resume with `-r` to continue where you left off
