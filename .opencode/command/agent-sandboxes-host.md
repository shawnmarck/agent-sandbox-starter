---
description: Exposes a port and returns a public URL
---

# Host Command

Use the `use_skill` tool to load the agent-sandboxes skill, then read and execute the workflow from `.claude/skills/agent-sandboxes/prompts/host.md`.

## Variables

- SANDBOX_ID: $1
- PORT: $2

## Instructions

1. First, use the `use_skill` tool to load the `agent-sandboxes` skill
2. Read the prompt file at `.claude/skills/agent-sandboxes/prompts/host.md`
3. Execute the workflow defined in that prompt with:
   - SANDBOX_ID set to: $1
   - PORT set to: $2

