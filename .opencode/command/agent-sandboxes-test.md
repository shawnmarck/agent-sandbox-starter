---
description: Runs validation tests against the hosted app including browser UI testing
---

Please run validation tests by following these steps:

1. Read the skill documentation at `.claude/skills/agent-sandboxes/SKILL.md`

2. Read the workflow prompt at `.claude/skills/agent-sandboxes/prompts/test.md`

3. Execute the workflow with these variables:
   - SANDBOX_ID: $1
   - URL: $2
   - PLAN_PATH: $3
   - WORKFLOW_ID: $4
