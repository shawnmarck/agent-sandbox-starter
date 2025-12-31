---
description: Generates a detailed implementation plan with Browser UI Testing workflows
---

# Plan-Full-Stack Command

Use the `use_skill` tool to load the agent-sandboxes skill, then read and execute the workflow from `.claude/skills/agent-sandboxes/prompts/plan-full-stack.md`.

## Variables

- USER_PROMPT: $ARGUMENTS

## Instructions

1. First, use the `use_skill` tool to load the `agent-sandboxes` skill
2. Read the prompt file at `.claude/skills/agent-sandboxes/prompts/plan-full-stack.md`
3. Execute the workflow defined in that prompt with:
   - USER_PROMPT set to: $ARGUMENTS

