---
description: Execute browser UI testing workflows from a plan file against any URL
---

Please execute browser UI testing by following these steps:

1. Read the plan file at: $2
2. Locate the "Browser UI Testing" section (or equivalent) that defines user story workflows
3. Change to the browser CLI directory: `.claude/skills/agent-sandboxes/sandbox_cli/`
4. Run `uv run sbx browser --help` to understand available browser commands
5. Generate a unique port for multi-agent safety: `BROWSER_PORT=$((9222 + $RANDOM % 778))`
6. Start the browser: `uv run sbx browser start --port $BROWSER_PORT`
7. Navigate to the URL: $1
8. Execute each user story workflow from the plan, taking screenshots on success/error
9. After all workflows complete, close ONLY your browser: `uv run sbx browser close --port $BROWSER_PORT`
10. Generate a report with results

Variables:
- URL: $1
- PLAN_FILE: $2
- PARALLEL: $3 (default: false)
- HEADED: $4 (default: false - add --headed flag if true)
