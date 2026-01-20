# Engineering Rules

This project supports multiple AI agent harnesses. Read the appropriate file for your tool:

- **OpenCode** (recommended): Read @OPENCODE.md for slash command syntax (`/command`)
- **Claude Code**: Read @CLAUDE.md for backslash command syntax (`\command`)
- **Gemini CLI**: Read @CLAUDE.md (same syntax as Claude Code)
- **Codex CLI**: Read @CLAUDE.md (same syntax as Claude Code)

## Skills

All tools automatically discover skills from `.claude/skills/`. The agent-sandboxes skill is located at:
`.claude/skills/agent-sandboxes/SKILL.md`

## Quick Reference

| Tool | Recommended | Command Prefix | Example |
|------|-------------|---------------|---------|
| **OpenCode** | ✅ Yes | `/` | `/sandbox run python hello.py` |
| Claude Code | | `\` | `\sandbox run python hello.py` |
| Gemini CLI | | `\` | `\sandbox run python hello.py` |
| Codex CLI | | `\` | `\sandbox run python hello.py` |
