# Purpose

Follow the `Workflow` to understand the codebase and instructions then report your understanding.

## Workflow

@README.md
@.claude/skills/agent-sandboxes/SKILL.md
@.claude/skills/agent-sandboxes/sandbox_cli/src/main.py
@.claude/skills/agent-sandboxes/prompts
@.opencode/oh-my-opencode.json
!`git ls-files`

## Report

Your understanding of the codebase and instructions.

**OpenCode Integration Notes**:
- Sisyphus (GLM-4.7) should use E2B sandboxes for all code execution
- Subagents automatically inherit sandbox context from Sisyphus
- Validate E2B_API_KEY in .env before sandbox operations
- All code execution happens in E2B cloud, never locally
- Use `\sisyphus-sandbox <prompt>` to initialize sandbox for workflows
- Subagents use `\subagent-sandbox <sandbox_id> <prompt>` for isolated execution
