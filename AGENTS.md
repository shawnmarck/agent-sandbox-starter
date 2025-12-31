# Engineering Rules

This project supports multiple AI agent harnesses. Read the appropriate file for your tool:

- **Claude Code**: Read @CLAUDE.md for backslash command syntax (`\command`)
- **OpenCode**: Read @OPENCODE.md for custom command syntax (`/command`)
- **Gemini CLI**: Read @CLAUDE.md (same syntax as Claude Code)
- **Codex CLI**: Read @CLAUDE.md (same syntax as Claude Code)

## Skills

The agent-sandboxes skill is located at `.claude/skills/agent-sandboxes/SKILL.md` and is automatically discovered by all supported tools.

## Quick Reference

| Tool | Command Prefix | Example |
|------|---------------|---------|
| Claude Code | `\` | `\sandbox run python hello.py` |
| OpenCode | `/` | `/sandbox run python hello.py` |
| Gemini CLI | `\` | `\sandbox run python hello.py` |
| Codex CLI | `\` | `\sandbox run python hello.py` |
