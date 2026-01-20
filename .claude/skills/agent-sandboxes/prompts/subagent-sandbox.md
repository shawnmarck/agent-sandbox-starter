---
argument-hint: [sandbox_id] [prompt]
---

# Purpose

Execute tasks in an existing E2B sandbox as a subagent (Librarian, Explore, Oracle, etc.). Use sandbox ID provided by Sisyphus for complete isolation.

## Variables

SANDBOX_ID: $1
PROMPT: $2
SANDBOX_CLI_PATH: `.claude/skills/agent-sandboxes/sandbox_cli/`

## Instructions

- Use the provided SANDBOX_ID for ALL operations
- Change to SANDBOX_CLI_PATH for all sbx commands
- NEVER initialize new sandbox - use the one provided
- Execute tasks safely in the existing sandbox
- Report results back to Sisyphus
- Do NOT delete sandbox - let Sisyphus manage lifecycle

## Workflow

### 1. Receive Sandbox Context

Sisyphus will provide:
```markdown
**Sandbox ID**: sbx_abc123def456
**Sandbox CLI Path**: .claude/skills/agent-sandboxes/sandbox_cli/
**Timeout**: 3600 seconds

Your task: [PROMPT]
```

Store these in your context as:
```javascript
sandbox_id = "sbx_abc123def456"
sandbox_cli_path = ".claude/skills/agent-sandboxes/sandbox_cli/"
```

### 2. Execute Task in Sandbox

Based on the PROMPT, execute the task using the provided sandbox:

**For running commands**:
```bash
uv run sbx exec SANDBOX_ID "command"
```

**For file operations**:
```bash
# Read file
uv run sbx files read SANDBOX_ID /home/user/file.txt

# Write file
uv run sbx files write SANDBOX_ID /home/user/file.txt "content"

# Upload local file
uv run sbx files upload SANDBOX_ID ./local_file.txt /home/user/file.txt
```

**For installing packages**:
```bash
# Install npm package
uv run sbx exec SANDBOX_ID "npm install package-name" --timeout 300

# Install Python package
uv run sbx exec SANDBOX_ID "pip install package-name" --timeout 300
```

**For running tests**:
```bash
# Run npm tests
uv run sbx exec SANDBOX_ID "npm test" --cwd /home/user/project

# Run Python tests
uv run sbx exec SANDBOX_ID "python -m pytest" --cwd /home/user/project
```

**For building applications**:
```bash
# Build project
uv run sbx exec SANDBOX_ID "npm run build" --timeout 300

# Start server in background
uv run sbx exec SANDBOX_ID "npm run dev -- --port 5173" --background --cwd /home/user/project
```

**For browser automation**:
```bash
# Start browser (use unique port for multi-agent safety)
uv run sbx browser start --port PORT

# Navigate to URL
uv run sbx browser nav "https://app-url.com" --port PORT

# Take screenshot
uv run sbx browser screenshot --path /tmp/screenshot.png --port PORT

# Close browser
uv run sbx browser close --port PORT
```

### 3. Handle Errors

If you encounter an error:

1. **Check if sandbox is still alive**:
   ```bash
   uv run sbx sandbox info SANDBOX_ID
   ```

2. **Reinitialize if needed** (but DON'T delete old one):
   - Inform Sisyphus that sandbox may need reinitialization
   - Provide error details
   - Let Sisyphus decide whether to create new sandbox

3. **Retry failed operations**:
   - Some operations may fail due to timeout
   - Retry with extended timeout if needed
   - Report persistent failures to Sisyphus

### 4. Report Results to Sisyphus

Provide structured report back to Sisyphus:

```markdown
## Task Execution Results

**Sandbox Used**: SANDBOX_ID
**Task**: [PROMPT]

### Actions Taken
[List what you did in the sandbox]

1. [Action 1]
2. [Action 2]
3. [Action 3]

### Results
[Output/results of the task]

[Command outputs, test results, etc.]

### Files Modified
[Files you created or modified in sandbox]

- /home/user/file.py
- /home/user/app.js

### Status
- ✅ Success: [What succeeded]
- ❌ Failed: [What failed]
- ⚠️ Warnings: [Any issues]

### Recommendations
[Next steps or follow-up actions]

[If task completed, any recommendations]
```

## Common Patterns

### Pattern 1: Research Task

**Purpose**: Investigate something in sandbox
**Action**:
```bash
# Read existing files in sandbox
uv run sbx files read SANDBOX_ID /home/user/project/file.py

# Analyze or process
uv run sbx exec SANDBOX_ID "python analyze.py" --timeout 300
```

**Report**: Your findings and analysis

### Pattern 2: Code Execution Task

**Purpose**: Run code or scripts in sandbox
**Action**:
```bash
# Write script to sandbox
uv run sbx files write SANDBOX_ID /home/user/script.py "#!/usr/bin/env python3
print('Hello from sandbox!')"

# Execute script
uv run sbx exec SANDBOX_ID "python /home/user/script.py"
```

**Report**: Script output and results

### Pattern 3: Build Task

**Purpose**: Build application in sandbox
**Action**:
```bash
# Install dependencies
uv run sbx exec SANDBOX_ID "npm install" --timeout 300

# Build project
uv run sbx exec SANDBOX_ID "npm run build" --timeout 300

# Run tests
uv run sbx exec SANDBOX_ID "npm test"
```

**Report**: Build output, test results, any warnings

### Pattern 4: Test Task

**Purpose**: Run tests in sandbox
**Action**:
```bash
# Run test suite
uv run sbx exec SANDBOX_ID "npm test" --cwd /home/user/project

# Get coverage
uv run sbx exec SANDBOX_ID "npm run coverage" --cwd /home/user/project
```

**Report**: Test results, coverage, failed tests

### Pattern 5: Parallel Task

**Purpose**: Execute task alongside other subagents
**Action**:
```bash
# Use the same sandbox ID provided by Sisyphus
# Each subagent operates independently but shares the sandbox
uv run sbx exec SANDBOX_ID "npm test" --cwd /home/user/module-a
```

**Report**: Your specific results, coordination with other agents

## Safety Rules

1. **ALWAYS use provided SANDBOX_ID** - Never create new sandbox
2. **NEVER delete sandbox** - Let Sisyphus manage lifecycle
3. **USE --timeout flags** - Prevent hanging operations
4. **Use unique ports for browser** - Prevent conflicts with other subagents
5. **Report errors clearly** - Let Sisyphus make decisions about retry/reinitialize
6. **NEVER run code locally** - All work must happen in sandbox

## Multi-Agent Safety

When multiple subagents use the same sandbox:

**Port Isolation**:
```bash
# Each subagent gets unique port for browser
Agent 1: PORT=9223
Agent 2: PORT=9224
Agent 3: PORT=9225
```

**File Coordination**:
- Avoid write conflicts by working in different directories
- Use file paths like `/home/user/agent1/` for subagent 1
- Use file paths like `/home/user/agent2/` for subagent 2

**Communication**:
- Report progress clearly so Sisyphus can coordinate
- Don't wait on other subagents - work independently
- Aggregate results to Sisyphus

## Report

Return to Sisyphus:

1. **Sandbox ID**: The sandbox ID you used
2. **Task Completed**: What you accomplished
3. **Results**: Output/data from the task
4. **Files**: Files created/modified
5. **Errors**: Any issues encountered
6. **Status**: Success/Failed
7. **Recommendations**: Next steps or follow-up

### Example Report

```markdown
## Subagent Task Report

**Sandbox**: sbx_abc123def456
**Task**: Run unit tests for authentication module

### Actions Taken
1. Installed test dependencies: npm install --timeout 300
2. Ran test suite: npm test
3. Generated coverage report: npm run coverage

### Results
All tests passed (15/15)
Coverage: 92%

### Files Modified
- /home/user/coverage.json
- /home/user/coverage/lcov-report/index.html

### Status
✅ Success - All tests passed with 92% coverage

### Recommendations
Consider adding integration tests for edge cases.
```
