---
description: Runs validation tests against the hosted app including browser UI testing
---

# Test Command

Use the `use_skill` tool to load the agent-sandboxes skill, then read and execute the workflow from `.claude/skills/agent-sandboxes/prompts/test.md`.

## Variables

- SANDBOX_ID: $1
- URL: $2
- PLAN_PATH: $3
- WORKFLOW_ID: $4

## Instructions

1. First, use the `use_skill` tool to load the `agent-sandboxes` skill
2. Read the prompt file at `.claude/skills/agent-sandboxes/prompts/test.md`
3. Execute the workflow defined in that prompt with:
   - SANDBOX_ID set to: $1
   - URL set to: $2
   - PLAN_PATH set to: $3
   - WORKFLOW_ID set to: $4

