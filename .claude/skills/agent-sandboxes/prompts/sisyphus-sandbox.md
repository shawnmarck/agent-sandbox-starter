---
argument-hint: [prompt]
---

# Purpose

Initialize and manage E2B sandboxes for Sisyphus (GLM-4.7) workflows. Automatically creates sandbox, captures ID, and passes to subagents for complete isolation.

## Variables

PROMPT: $1
SANDBOX_CLI_PATH: `.claude/skills/agent-sandboxes/sandbox_cli/`
ENVIRONMENT_FILE_PATH: `../../../../.env`
TIMEOUT_DURATION_IN_SECONDS: `3600`

## Instructions

- Validate E2B_API_KEY before proceeding
- Change to SANDBOX_CLI_PATH for all sbx commands
- Initialize sandbox with: `uv run sbx init --timeout TIMEOUT_DURATION_IN_SECONDS`
- Capture sandbox ID from output
- Store sandbox ID in memory context for subagents
- Pass sandbox ID to all subagent prompts
- Never delete sandbox unless explicitly requested
- Use E2B sandboxes for ALL code execution (no local bash for code running)

## Workflow

### 1. Validate Environment

First, verify E2B_API_KEY is set:

```bash
cd SANDBOX_CLI_PATH
grep "E2B_API_KEY" $(cat ENVIRONMENT_FILE_PATH)
```

If missing, stop and instruct user:
```
Error: E2B_API_KEY not found in .env file

Please add your E2B API key to the .env file in the project root:
echo "E2B_API_KEY=sbx_..." >> .env

Get your API key from: https://e2b.dev/dashboard/keys
```

### 2. Initialize Sandbox

Change to CLI directory and initialize sandbox:

```bash
cd SANDBOX_CLI_PATH
uv run sbx init --timeout TIMEOUT_DURATION_IN_SECONDS
```

**Output Example**:
```
Created sandbox: sbx_abc123def456
```

**CRITICAL**: Capture this ID! Store it in your memory context as:
```javascript
sandbox_id = "sbx_abc123def456"
```

### 3. Report to User

Inform user about sandbox initialization:

```markdown
✓ **Sandbox initialized for Sisyphus workflow**

- **Sandbox ID**: sbx_abc123def456
- **Timeout**: 3600 seconds (1 hour)
- **Isolation**: All code execution in E2B cloud environment

All subsequent code execution will be isolated in this sandbox.
```

### 4. Execute Task in Sandbox

Based on the user's PROMPT, execute the task using the sandbox:

**For running commands**:
```bash
uv run sbx exec sandbox_id "your command here"
```

**For file operations**:
```bash
# Write file
uv run sbx files write sandbox_id /home/user/app.py "print('hello')"

# Read file
uv run sbx files read sandbox_id /home/user/app.py

# Upload file
uv run sbx files upload sandbox_id ./local_file.py /home/user/file.py
```

**For building applications**:
```bash
# Install dependencies
uv run sbx exec sandbox_id "npm install" --timeout 300

# Build project
uv run sbx exec sandbox_id "npm run build" --timeout 300

# Start server in background
uv run sbx exec sandbox_id "npm run dev -- --port 5173" --background
```

### 5. Delegate to Subagents

When Sisyphus needs to delegate to subagents (Librarian, Explore, Oracle), include sandbox context in their prompts:

```markdown
### Sandbox Context for Subagent

**Sandbox ID**: sbx_abc123def456
**Sandbox CLI Path**: .claude/skills/agent-sandboxes/sandbox_cli/
**Timeout**: 3600 seconds

Use this sandbox for ALL code execution:
- Running commands
- Installing packages
- Testing code
- Building applications
- Browser automation

Do NOT execute code locally - all work must happen in the sandbox.
```

### 6. Get Public URL (If Hosting Frontend)

If the task involves hosting a frontend application:

```bash
# Get the public URL for port 5173
uv run sbx sandbox get-host sandbox_id --port 5173
```

**Output Example**:
```
https://5173-sbx_abc123def456.e2b.app
```

Return this URL to the user.

### 7. Close Browser (If Used)

If browser automation was used:

```bash
# Close the browser (specifying port for multi-agent safety)
uv run sbx browser close --port PORT
```

**CRITICAL**: Always specify `--port PORT` when multiple agents are using browser automation.

## Common Patterns

### Pattern 1: Build and Test Application

```bash
# Initialize sandbox
uv run sbx init --timeout 3600
# Capture: sandbox_id = "sbx_..."

# Build app
uv run sbx exec sandbox_id "npm install"
uv run sbx exec sandbox_id "npm run build"

# Run tests
uv run sbx exec sandbox_id "npm test"

# Start server
uv run sbx exec sandbox_id "npm run dev -- --port 5173" --background

# Get URL
uv run sbx sandbox get-host sandbox_id --port 5173
```

### Pattern 2: Run Python Script

```bash
# Write script to sandbox
uv run sbx files write sandbox_id /home/user/test.py "import os; print(os.getcwd())"

# Execute script
uv run sbx exec sandbox_id "python /home/user/test.py"

# Read output
uv run sbx exec sandbox_id "cat /tmp/output.txt"
```

### Pattern 3: Parallel Subagent Execution

When Sisyphus launches multiple subagents, each needs its own sandbox ID:

```markdown
### Subagent 1: Librarian
**Sandbox ID**: sbx_lib123
**Task**: Research authentication patterns
**Use**: Use sbx_lib123 for all file operations

### Subagent 2: Explore
**Sandbox ID**: sbx_exp456
**Task**: Analyze existing codebase
**Use**: Use sbx_exp456 for all file operations
```

## Safety Rules

1. **NEVER run code locally** - Always use `sbx exec sandbox_id "command"`
2. **CAPTURE sandbox ID** - Store in memory, not files or shell variables
3. **USE --timeout** - Always specify timeout to prevent hanging
4. **UNIQUE IDs per agent** - Each subagent gets unique sandbox
5. **PORT isolation** - Use unique ports (9222-9999) for browser automation
6. **NEVER delete sandbox** - Let it auto-terminate after timeout unless asked
7. **Validate E2B_API_KEY** - Check before any sandbox operations

## Report

Return to the user:

1. **Sandbox ID**: The sandbox ID created
2. **Task Execution**: What was done in the sandbox
3. **Results**: Output of the task
4. **URL** (if applicable): Public URL of hosted application
5. **Next Steps**: Any recommended follow-up actions

### Example Report

```markdown
✓ **Sandbox Workflow Complete**

**Sandbox**: sbx_abc123def456 (auto-terminates in 3600 seconds)

**Task Executed**:
- Initialized E2B sandbox
- Built application in isolation
- Ran all tests
- Hosted frontend on port 5173

**Application URL**: https://5173-sbx_abc123def456.e2b.app

The sandbox will automatically cleanup after 1 hour. No manual cleanup needed.
```

## Troubleshooting

### "E2B_API_KEY not found"

```bash
# Add API key to .env
echo "E2B_API_KEY=your_actual_key" >> .env
```

### "Sandbox timeout"

Increase timeout:
```bash
uv run sbx init --timeout 7200  # 2 hours
uv run sbx exec sandbox_id "command" --timeout 300
```

### "Sandbox not found"

Check sandbox ID is correct. Sandboxes auto-terminate after timeout. Reinitialize if needed:

```bash
uv run sbx init --timeout 3600
# Capture new sandbox ID
```

### Browser automation fails

Install Playwright:
```bash
uv run sbx exec sandbox_id "uv pip install playwright" --timeout 300
uv run sbx exec sandbox_id "uv run playwright install chromium" --timeout 300
```

Use `--port PORT` for multi-agent safety.
