---
description: Complete full-stack workflow - initialize sandbox, plan, build, and host with public URL
---

# Plan-Build-Host-Test Command

Use the `use_skill` tool to load the agent-sandboxes skill, then read and execute the workflow from `.claude/skills/agent-sandboxes/prompts/plan-build-host-test.md`.

## Variables

- USER_PROMPT: $1
- WORKFLOW_ID: $2 (default: "workflow-<hhmmss>+<uuid>" if not provided)

## Instructions

1. First, use the `use_skill` tool to load the `agent-sandboxes` skill
2. Read the prompt file at `.claude/skills/agent-sandboxes/prompts/plan-build-host-test.md`
3. Execute the workflow defined in that prompt with:
   - USER_PROMPT set to: $1
   - WORKFLOW_ID set to: $2 (or generate default if not provided)

