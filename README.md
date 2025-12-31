# Claude Code Project Template

A starter template for Claude Code projects with E2B sandbox integration and custom skills.

## What's Included

This template provides:
- **Agent Sandboxes Skill**: Safe, isolated code execution using E2B sandboxes
- **Custom Commands**: Generic browser testing and prime/initialization commands
- **Multi-Tool Support**: Instructions for Claude Code, Gemini CLI, and Codex CLI
- **Ready-to-Use Structure**: `.claude/` folder with skills and commands pre-configured

## Quick Start

1. **Clone this template**:
   ```bash
   git clone <your-repo-url>
   cd <project-name>
   ```

2. **Configure E2B** (for sandbox features):
   ```bash
   cp .env.sample .env
   # Add your E2B API key to .env
   echo "E2B_API_KEY=sbx_..." >> .env
   ```
   Get your key from [E2B Dashboard](https://e2b.dev/dashboard/keys)

3. **Start using**:
   ```bash
   claude  # or gemini, or codex
   ```

## Skills & Commands

### Agent Sandboxes Skill
Run code, build apps, and execute commands in isolated E2B sandboxes.

**Documentation**: See `.claude/skills/agent-sandboxes/SKILL.md` for full details

**Quick commands**:
- `\sandbox <prompt>` - Ad-hoc sandbox operations
- `\agent-sandboxes:plan-build-host-test <prompt> <workflow_id>` - Full app lifecycle

### Custom Commands
- `/prime` - Initialize and understand the project
- `/generic-browser-test` - Browser automation workflows

## Customizing This Template

### Add New Skills
Place new skills in `.claude/skills/<skill-name>/`:
```
.claude/skills/<skill-name>/
├── SKILL.md           # Skill definition
├── prompts/          # Workflow prompts
└── <other files>
```

### Add New Commands
Place commands in `.claude/commands/`:
- Single file: `.claude/commands/<command>.md`
- Nested: `.claude/commands/<category>/<command>.md`

## Tool-Specific Instructions

- **CLAUDE.md**: Claude Code-specific instructions
- **AGENTS.md**: General agent instructions
- **GEMINI.md**: Gemini CLI-specific instructions

## Prerequisites

- Python >= 3.12
- `uv` package manager (for sandbox CLI)
- E2B account (for sandbox features)
- Claude Code / Gemini CLI / Codex CLI

## License

[Your License Here]

## Learn More

- [E2B Documentation](https://e2b.dev/docs)
- [Claude Code Documentation](https://github.com/anthropics/claude-code)
