# Agentic Project Template

A starter template for AI coding agents with E2B sandbox integration and custom skills.

> **Attribution**: This project template is based on [agent-sandbox-skill](https://github.com/disler/agent-sandbox-skill/) by [@disler](https://github.com/disler). The original repository provided the foundation for the E2B sandbox integration and skill structure.

## What's Included

This template provides:
- **Agent Sandboxes Skill**: Safe, isolated code execution using E2B sandboxes
- **Custom Commands**: Generic browser testing and prime/initialization commands
- **Multi-Tool Support**: Instructions for Claude Code, Gemini CLI, Codex CLI, and OpenCode
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
   claude    # Claude Code
   opencode  # OpenCode
   gemini    # Gemini CLI
   codex     # Codex CLI
   ```

## Skills & Commands

### Agent Sandboxes Skill
Run code, build apps, and execute commands in isolated E2B sandboxes.

**Documentation**: See `.claude/skills/agent-sandboxes/SKILL.md` for full details

**Quick commands** (syntax varies by tool):

| Tool | Sandbox Command | Full Workflow Command |
|------|-----------------|----------------------|
| Claude Code | `\sandbox <prompt>` | `\agent-sandboxes:plan-build-host-test <prompt> <id>` |
| OpenCode | `/sandbox <prompt>` | `/agent-sandboxes-plan-build-host-test <prompt> <id>` |
| Gemini CLI | `\sandbox <prompt>` | `\agent-sandboxes:plan-build-host-test <prompt> <id>` |
| Codex CLI | `\sandbox <prompt>` | `\agent-sandboxes:plan-build-host-test <prompt> <id>` |

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

**For Claude/Gemini/Codex** - Place in `.claude/commands/`:
- Single file: `.claude/commands/<command>.md`
- Nested: `.claude/commands/<category>/<command>.md`

**For OpenCode** - Place in `.opencode/command/`:
- Single file: `.opencode/command/<command>.md`

## Tool-Specific Instructions

- **CLAUDE.md**: Claude Code-specific instructions (backslash commands)
- **OPENCODE.md**: OpenCode-specific instructions (custom commands)
- **AGENTS.md**: General agent instructions
- **GEMINI.md**: Gemini CLI-specific instructions

## Prerequisites

- Python >= 3.12
- `uv` package manager (for sandbox CLI)
- E2B account (for sandbox features)
- Claude Code / OpenCode / Gemini CLI / Codex CLI

## Credits

This project template is based on the excellent work by:
- **[@disler](https://github.com/disler)** - [agent-sandbox-skill](https://github.com/disler/agent-sandbox-skill/) - Original E2B sandbox skill implementation

## Learn More

- [E2B Documentation](https://e2b.dev/docs)
- [Claude Code Documentation](https://github.com/anthropics/claude-code)
- [OpenCode Documentation](https://opencode.ai/docs)
- [Original Agent Sandbox Skill](https://github.com/disler/agent-sandbox-skill/)
