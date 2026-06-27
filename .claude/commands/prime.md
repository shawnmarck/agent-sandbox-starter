# Purpose

Follow the `Workflow` to understand the codebase and instructions then report your understanding.

## Workflow

@README.md
@.claude/skills/agent-sandboxes/SKILL.md
@.claude/skills/agent-sandboxes/sandbox_cli/src/main.py
@.claude/skills/agent-sandboxes/prompts
@opencode.json
!`git ls-files`

## Report

Your understanding of the codebase and instructions.

**OpenCode Integration Notes**:
- Use E2B sandboxes for all code execution when the skill applies
- Validate E2B_API_KEY in `.env` before sandbox operations (copy from `.env.sample`)
- All sandbox code execution runs in E2B cloud, not on the host filesystem
- Use `\sisyphus-sandbox <prompt>` to initialize a sandbox for workflows
- Subagents use `\subagent-sandbox <sandbox_id> <prompt>` for isolated execution
