---
description: Execute browser UI testing workflows from a plan file against any URL
---

# Purpose

Execute user story workflows defined in a plan file to validate UI functionality through browser automation against any public URL. Supports optional parallel execution of workflows.

## Variables

- URL: $1
- PLAN_FILE: $2
- PARALLEL: $3 (default: false)
- HEADED: $4 (default: false)
- OUTPUT_DIR: `temp/generic-browser-test/`
- BROWSER_CLI_PATH: `.claude/skills/agent-sandboxes/sandbox_cli/`

## Instructions

- CRITICAL: Re-read `PLAN_FILE` and locate a `Browser UI Testing` section or equivalent that defines the user story workflows to test.
- Execute ALL user story workflows from that section in order
- On ANY error, stop immediately, debug the issue, and report it
- Take screenshots on success and error states
- If `PARALLEL` is `true`, execute each workflow in a separate subagent
- If `PARALLEL` is `false` (default), execute workflows sequentially
- This command does NOT require a sandbox - it tests against any accessible URL
- ALWAYS run `uv run sbx browser --help` first to understand available browser commands
- Generate unique browser port for multi-agent safety
- If `HEADED` is `true`, start browser with `--headed` flag (visible window)
- If `HEADED` is `false` (default), run headless (no window)
- **CRITICAL - PORT ISOLATION**: Each agent/subagent MUST use its own unique port. NEVER close browsers on ports you didn't start.
- **CRITICAL - BROWSER CLOSE**: When closing browser, ALWAYS specify `--port $BROWSER_PORT`. This ensures you ONLY close YOUR browser, not other agents' browsers.

## Workflow

### 1. Setup

1. Change to `BROWSER_CLI_PATH` directory for all browser commands
2. Run `uv run sbx browser --help` to understand available commands and options
3. Re-read the plan file at `PLAN_FILE` and locate a `Browser UI Testing` section
4. Extract all `User Story Workflow <N>: <Feature Name>` entries
5. Generate unique port for multi-agent safety: `BROWSER_PORT=$((9222 + $RANDOM % 778))`
6. Create output directory if needed: `mkdir -p temp/generic-browser-test/`

### 2. Determine Execution Mode

**If PARALLEL is false (default):** Proceed to Step 3 (Sequential Execution)
**If PARALLEL is true:** Skip to Step 4 (Parallel Execution)

### 3. Sequential Execution

1. Start browser:
   - If HEADED is true: `uv run sbx browser start --headed --port $BROWSER_PORT`
   - If HEADED is false: `uv run sbx browser start --port $BROWSER_PORT`
2. If Playwright is not installed:
   - `uv pip install playwright`
   - `uv run playwright install chromium`

For EACH `### User Story Workflow <N>: <Feature Name>` in the plan:

**Execute the workflow:**
- Navigate to URL: `uv run sbx browser nav [URL] --port $BROWSER_PORT`
- Execute each step using appropriate browser commands:
  - **Open URL**: `uv run sbx browser nav <url> --port $BROWSER_PORT`
  - **Click**: `uv run sbx browser click "<selector>" --port $BROWSER_PORT`
  - **Type/Fill**: `uv run sbx browser type "<selector>" "<text>" --port $BROWSER_PORT`
  - **Scroll**: `uv run sbx browser scroll <direction> --port $BROWSER_PORT`
  - **Evaluate JS**: `uv run sbx browser eval "<js code>" --port $BROWSER_PORT`
  - **Screenshot**: `uv run sbx browser screenshot --path <path> --port $BROWSER_PORT`
  - **Get DOM**: `uv run sbx browser dom --port $BROWSER_PORT`
  - **Get A11y Tree**: `uv run sbx browser a11y --port $BROWSER_PORT`
- **CONFIRM steps**: Verify expected outcome using `browser eval` or `browser screenshot`

**On ERROR:**
- Take error screenshot: `uv run sbx browser screenshot --path [OUTPUT_DIR]/<workflow-name>-error.png --port $BROWSER_PORT`
- Document the exact step that failed
- Continue to next workflow (do not block other tests)

**On SUCCESS:**
- Take final screenshot: `uv run sbx browser screenshot --path [OUTPUT_DIR]/<workflow-name>-success.png --port $BROWSER_PORT`
- Mark workflow as PASSED

After all workflows complete:
- **CRITICAL**: Close ONLY your browser: `uv run sbx browser close --port $BROWSER_PORT`
- Skip to Step 5 (Report)

### 4. Parallel Execution (PARALLEL=true)

**CRITICAL - PORT ASSIGNMENT FOR PARALLEL MODE:**
- Assign DETERMINISTIC ports to each workflow based on index: `9222 + workflow_index`
- Workflow 1 gets port 9223, Workflow 2 gets port 9224, etc.
- DO NOT use `$RANDOM` in parallel mode - it causes collisions

For EACH workflow, launch a subagent with:
- Unique port assignment
- The workflow steps to execute
- Instructions to close ONLY their assigned port

Wait for all subagents to complete and collect their results.

### 5. Report

Generate and save report to `temp/generic-browser-test/<plan-name>_validation_results.md` with:
- URL Tested
- Plan File
- Execution Mode (Sequential or Parallel)
- Browser Mode (Headless or Headed)
- Workflow Results (status, steps executed, screenshots)
- Summary (total, passed, failed, pass rate)

