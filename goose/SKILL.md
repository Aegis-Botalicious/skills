---
name: goose
description: Use Goose AI agent for code execution, building, and implementation. Triggers when executing implementation plans, coding tasks, file operations, testing, and multi-model development work. Use for DO phase after planning with Claude Code. Supports multi-model configuration (Opus, Codex, local LLM).
---

# Goose CLI

Use `goose` for execution, coding, and building. Receives plans from Claude Code.

## Quick Start

### Interactive Session

```bash
cd ~/project
goose session
```

### Named Session (Resumable)

```bash
goose session --name "habityield-auth"
goose session --name "habityield-auth" --resume  # Continue later
```

### Execute from File

```bash
goose run instructions.md
echo "Build the auth module" | goose run
```

## Configuration

### First Time Setup

```bash
goose configure
```

This opens interactive config for:
- Provider selection (Anthropic, OpenAI, etc.)
- Model selection
- API keys

### Multi-Model Config

Edit `~/.config/goose/profiles.yaml`:

```yaml
default:
  provider: anthropic
  model: claude-sonnet-4-20250514
  
opus:
  provider: anthropic
  model: claude-opus-4-20250514
  
codex:
  provider: openai
  model: gpt-5.2-codex
  
local:
  provider: ollama
  model: codellama:latest
  host: http://jetson-orin:11434
```

Use profiles:
```bash
GOOSE_PROFILE=opus goose session
GOOSE_PROFILE=codex goose session --name "code-gen"
GOOSE_PROFILE=local goose session --name "quick-task"
```

## Extensions (MCP)

### Built-in Extensions

```bash
# List available
goose mcp list

# Add to session
goose session --with-builtin developer,memory
```

### Custom Extensions

```bash
goose session --with-extension "npx -y @modelcontextprotocol/server-filesystem /path"
```

## Workflow: Executing Plans

### 1. Receive Plan from Claude Code

```bash
cat docs/IMPLEMENTATION_PLAN.md
```

### 2. Start Execution Session

```bash
goose session --name "feature-implementation"
```

### 3. Execute Step by Step

In session:
```
Execute step 1 from docs/IMPLEMENTATION_PLAN.md:
"Set up Firebase Auth with email/password"

Commit after completion with message: "feat(auth): add Firebase Auth setup"
```

### 4. Continue or Resume

```bash
goose session --name "feature-implementation" --resume
```

## Running in Background

For autonomous execution:

```bash
# Start with PTY
exec pty:true workdir:~/project background:true command:"goose session --name 'overnight-build'"

# Monitor
process action:log sessionId:XXX

# Send input
process action:submit sessionId:XXX data:"Continue to step 2"

# Kill if needed
process action:kill sessionId:XXX
```

## Session Management

```bash
goose session list              # List all sessions
goose session remove            # Interactive remove
goose session export            # Export session
goose projects                  # List recent project dirs
```

## Multi-Model Strategy

| Task Type | Profile | Model | Why |
|-----------|---------|-------|-----|
| Complex reasoning | opus | claude-opus-4 | Best for architecture |
| Code generation | codex | gpt-5.2-codex | Optimized for code |
| Quick tasks | local | codellama | Free, fast |
| General dev | default | claude-sonnet-4 | Good balance |

### Example: Multi-Model Workflow

```bash
# 1. Architecture with Opus
GOOSE_PROFILE=opus goose session --name "design"
> Design the database schema for HabitYield

# 2. Code gen with Codex  
GOOSE_PROFILE=codex goose session --name "implement"
> Implement the schema from docs/SCHEMA.md

# 3. Quick fixes with local
GOOSE_PROFILE=local goose session --name "fixes"
> Fix the linting errors in lib/models/
```

## Integration with Claude Code

### The THINK → DO Pattern

```
┌─────────────────┐     ┌─────────────────┐
│   Claude Code   │ ──▶ │     Goose       │
│   (THINK)       │     │     (DO)        │
├─────────────────┤     ├─────────────────┤
│ /plan mode      │     │ Multi-model     │
│ Architecture    │     │ Code execution  │
│ PRD refinement  │     │ Testing         │
│ Design docs     │     │ Building        │
└─────────────────┘     └─────────────────┘
        │                       │
        ▼                       ▼
  IMPLEMENTATION_PLAN.md   Working Code
```

## Tips

- Use named sessions for complex features
- Match model to task complexity
- Resume sessions to maintain context
- Use `goose run` for scripted execution
- Configure local LLM for cost savings
