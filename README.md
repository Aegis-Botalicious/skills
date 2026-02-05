# Skills 🛠️

Custom OpenClaw skills for the THINK → DO workflow.

## Available Skills

| Skill | Purpose |
|-------|---------|
| [claude-code](./claude-code/) | Planning, architecture, design with Claude Code CLI |
| [goose](./goose/) | Code execution with Goose (multi-model support) |
| [todoist](./todoist/) | Task management with Todoist |

## The THINK → DO Pattern

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
```

## Installation

Clone to your OpenClaw workspace:

```bash
git clone https://github.com/Aegis-Botalicious/skills.git ~/.openclaw/workspace/skills
```

Or symlink individual skills:

```bash
ln -s /path/to/skills/claude-code ~/.openclaw/workspace/skills/
```

## License

MIT
