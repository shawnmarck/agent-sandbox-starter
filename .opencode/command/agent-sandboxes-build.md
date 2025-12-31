---
description: Executes a build plan within a sandbox
---

# Build Command

Use the `use_skill` tool to load the agent-sandboxes skill, then read and execute the workflow from `.claude/skills/agent-sandboxes/prompts/build.md`.

## Variables

- PLAN_PATH: $ARGUMENTS

## Instructions

1. First, use the `use_skill` tool to load the `agent-sandboxes` skill
2. Read the prompt file at `.claude/skills/agent-sandboxes/prompts/build.md`
3. Execute the workflow defined in that prompt with:
   - PLAN_PATH set to: $ARGUMENTS

