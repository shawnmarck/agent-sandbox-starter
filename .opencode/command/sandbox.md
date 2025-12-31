---
description: Small general sandbox operations. Adhoc prompt with minimal compute usage.
---

# Sandbox Command

Use the `use_skill` tool to load the agent-sandboxes skill, then read and execute the workflow from `.claude/skills/agent-sandboxes/prompts/sandbox.md`.

## Variables

- USER_REQUEST: $ARGUMENTS

## Instructions

1. First, use the `use_skill` tool to load the `agent-sandboxes` skill
2. Read the prompt file at `.claude/skills/agent-sandboxes/prompts/sandbox.md`
3. Execute the workflow defined in that prompt with the USER_REQUEST variable set to: $ARGUMENTS

