# OpenCode + E2B Sandbox Integration Plan

## Executive Summary

**Goal**: Integrate E2B sandbox capabilities into OpenCode with GLM-4.7 as the primary Sisyphus agent, ensuring all work is performed safely in isolated sandbox environments.

**Current State**:
- ✅ OpenCode installed and configured with GLM-4.7 (build/plan agents)
- ✅ Oh My OpenCode plugin installed
- ✅ E2B sandbox skill and CLI exist (from Claude Code template)
- ✅ OpenCode supports Claude-compatible skills
- ❌ No automatic sandbox execution for Sisyphus workflows

**Target State**:
- ✅ Sisyphus automatically uses E2B sandboxes for all code execution
- ✅ Subagents (Librarian, Explore, Oracle) can run in isolated sandboxes
- ✅ Safe, non-interactive command execution
- ✅ Documented workflows and usage patterns

---

## Architecture Overview

### Current Integration Points

```
OpenCode
├── Oh My OpenCode Plugin
│   ├── Sisyphus (GLM-4.7) - Main orchestrator
│   ├── Subagents (Librarian, Explore, Oracle, etc.)
│   └── Hook System (25+ automation hooks)
├── Custom Tools (TypeScript/JavaScript)
├── Agent Skills (.claude/skills/*/)
│   └── agent-sandboxes/ ← Existing E2B skill
└── MCP Servers
```

### Integration Approach

**Primary Method: OpenCode Agent Skills (Recommended)**
- Leverage existing `.claude/skills/agent-sandboxes/` structure
- OpenCode natively supports Claude-compatible skills
- Minimal code changes required
- Maintains compatibility with Claude Code

**Secondary Method: Custom Tool**
- Create TypeScript/JavaScript wrapper for E2B CLI
- More control over sandbox lifecycle
- Can add custom UI integration
- Requires additional development

---

## Implementation Plan

### Phase 1: Skill Enhancement (Immediate)

**Objective**: Make existing agent-sandboxes skill work seamlessly with OpenCode

#### 1.1 Update SKILL.md for OpenCode Compatibility

**File**: `.claude/skills/agent-sandboxes/SKILL.md`

**Changes Needed**:
- Add OpenCode-specific instructions
- Update hook system references
- Integrate with Oh My OpenCode workflow patterns

**Code Snippet**:
```markdown
---
name: agent-sandboxes
description: Operate E2B agent sandboxes using the CLI. Use when user needs to run code in isolation, test packages, execute commands safely, or work with binary files in a sandbox environment. Keywords: sandbox, e2b, isolated environment, run code, test code, safe execution, sisyphus, subagent.
compatibility:
  - opencode: "1.0+"
  - claude-code: "1.0+"
---

# Agent Sandboxes for OpenCode & Claude Code

This skill provides access to E2B sandboxes through a streamlined CLI for safe code execution and file operations in isolated environments.

## OpenCode Integration

### Automatic Sandbox Usage with Sisyphus

When using Sisyphus (GLM-4.7) with Oh My OpenCode, the skill automatically wraps code execution in sandboxes:

1. **Pre-Tool Hook**: Automatically initialize sandbox before bash/exec commands
2. **Post-Tool Hook**: Capture sandbox ID for persistence
3. **Cleanup Hook**: Optionally destroy sandbox after task completion

### Hook Configuration

Add to `.opencode/oh-my-opencode.json`:
```json
{
  "hooks": {
    "e2b-sandbox-initializer": {
      "enabled": true,
      "trigger": "PreToolUse",
      "tools": ["bash", "exec"],
      "action": "init_sandbox_if_needed"
    }
  }
}
```

[... rest of existing SKILL.md content ...]
```

#### 1.2 Create OpenCode-Specific Prompts

**New Files**:
- `.claude/skills/agent-sandboxes/prompts/sisyphus-sandbox.md`
- `.claude/skills/agent-sandboxes/prompts/subagent-sandbox.md`

**sisyphus-sandbox.md**:
```markdown
---
argument-hint: [prompt]
---

# Purpose

Initialize and manage E2B sandboxes for Sisyphus workflows. Automatically creates sandbox, captures ID, and passes to subagents.

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

## Workflow

### 1. Validate Environment

```bash
cd SANDBOX_CLI_PATH
grep "E2B_API_KEY" `cat ENVIRONMENT_FILE_PATH`
```

If missing, instruct user to add E2B_API_KEY to .env

### 2. Initialize Sandbox

```bash
uv run sbx init --timeout TIMEOUT_DURATION_IN_SECONDS
```

Output example: `Created sandbox: sbx_abc123def456`

**CRITICAL**: Capture this ID! Remember it as `sandbox_id = "sbx_abc123def456"`

### 3. Report to User

```
✓ Sandbox initialized for Sisyphus workflow

Sandbox ID: sbx_abc123def456
Timeout: 3600 seconds (1 hour)

All code execution will be isolated in this sandbox.
```

### 4. Delegation to Subagents

When launching subagents (Librarian, Explore, Oracle), include in prompt:

```
Sandbox ID: sbx_abc123def456
Sandbox CLI Path: .claude/skills/agent-sandboxes/sandbox_cli/

Use this sandbox for all code execution, file operations, and testing.
```

## Report

Return to user:
- Sandbox ID
- Timeout information
- Instructions for subagent delegation
```

#### 1.3 Update Commands for OpenCode

**File**: `.claude/commands/prime.md`

**Add OpenCode section**:
```markdown
# Purpose

Follow the `Workflow` to understand the codebase and instructions then report your understanding.

## Workflow

@README.md
@.claude/skills/agent-sandboxes/SKILL.md
@.claude/skills/agent-sandboxes/sandbox_cli/src/main.py
@.claude/skills/agent-sandboxes/prompts
@.opencode/oh-my-opencode.json
!`git ls-files`

## Report

Your understanding of the codebase and instructions.

**OpenCode Integration Notes**:
- Sisyphus should use E2B sandboxes for all code execution
- Subagents automatically inherit sandbox context
- Validate E2B_API_KEY in .env before sandbox operations
```

### Phase 2: Custom Tool Development (Optional Enhancement)

**Objective**: Create TypeScript wrapper for better OpenCode integration

#### 2.1 Create Tool Definition

**File**: `.opencode/tools/e2b-sandbox.ts`

```typescript
import { tool } from "@opencode-ai/plugin"

export default tool({
  name: "e2b-sandbox",
  description: "Initialize and manage E2B sandboxes for safe code execution",
  args: {
    action: tool.schema.enum(["init", "exec", "files", "browser"])
      .describe("Action to perform on sandbox")
      .default("init"),
    sandboxId: tool.schema.string()
      .describe("Sandbox ID (required for actions other than init)"),
    command: tool.schema.string()
      .describe("Command to execute (for exec action)"),
    timeout: tool.schema.number()
      .describe("Timeout in seconds")
      .default(3600),
  },
  async execute(args) {
    const { action, sandboxId, command, timeout } = args
    
    // Change to sandbox CLI directory
    const cliPath = ".claude/skills/agent-sandboxes/sandbox_cli/"
    
    if (action === "init") {
      // Initialize sandbox
      const result = await execCommand(
        `cd ${cliPath} && uv run sbx init --timeout ${timeout}`
      )
      return result
    }
    
    if (action === "exec" && sandboxId && command) {
      // Execute command in sandbox
      const result = await execCommand(
        `cd ${cliPath} && uv run sbx exec ${sandboxId} "${command}"`
      )
      return result
    }
    
    // Add more actions as needed...
  }
})

async function execCommand(cmd: string): Promise<string> {
  // Use OpenCode's bash tool or Node.js child_process
  // Implementation depends on OpenCode's tool API
  return "Result"
}
```

#### 2.2 Install Dependencies

```bash
cd .opencode
npm install @opencode-ai/plugin
```

### Phase 3: Configuration & Hooks

**Objective**: Configure Oh My OpenCode for automatic sandbox usage

#### 3.1 Update oh-my-opencode.json

**File**: `.opencode/oh-my-opencode.json` or `~/.config/opencode/oh-my-opencode.json`

```json
{
  "$schema": "https://raw.githubusercontent.com/code-yeongyu/oh-my-opencode/master/assets/oh-my-opencode.schema.json",
  "google_auth": false,
  "agents": {
    "librarian": {
      "model": "google/gemini-3-flash"
    },
    "explore": {
      "model": "opencode/grok-code"
    },
    "oracle": {
      "model": "anthropic/claude-opus-4-5"
    },
    "oracle-lite": {
      "model": "google/gemini-3-pro-medium"
    },
    "frontend-ui-ux-engineer": {
      "model": "google/gemini-3-pro-high"
    },
    "document-writer": {
      "model": "google/gemini-3-flash"
    },
    "multimodal-looker": {
      "model": "google/gemini-3-flash"
    }
  },
  "sandbox": {
    "enabled": true,
    "provider": "e2b",
    "auto_init": true,
    "default_timeout": 3600,
    "template": "claude-node22-python-sqlite"
  }
}
```

#### 3.2 Create Hook Script (Manual Implementation)

**File**: `.opencode/hooks/e2b-sandbox-hook.js`

Since Oh My OpenCode hooks are complex, we'll implement manual hooks through skill instructions:

```javascript
// This is a conceptual hook structure
// Actual implementation depends on Oh My OpenCode's hook API

module.exports = {
  name: "e2b-sandbox-initializer",
  trigger: "PreToolUse",
  execute: async (context) => {
    if (!context.sandboxId) {
      // Initialize sandbox
      const result = await initSandbox()
      context.sandboxId = result.sandboxId
    }
    return context
  }
}
```

### Phase 4: Documentation & Testing

#### 4.1 User Documentation

**File**: `docs/OPENCODE_E2B_USAGE.md`

```markdown
# Using E2B Sandboxes with OpenCode & GLM-4.7

## Quick Start

### 1. Configure Environment

```bash
# Ensure E2B API key is set
echo "E2B_API_KEY=sbx_..." >> .env
```

### 2. Verify Installation

```bash
# Test sandbox CLI
cd .claude/skills/agent-sandboxes/sandbox_cli
uv run sbx --help
```

### 3. Start OpenCode with Sisyphus

```bash
opencode
# Sisyphus will automatically use sandboxes for code execution
```

## Common Workflows

### Ad-hoc Sandbox Operations

```
User: "Quick test this Python script in a sandbox"
Sisyphus: 
1. Reads @.claude/skills/agent-sandboxes/SKILL.md
2. Initializes sandbox: sbx init --timeout 3600
3. Captures sandbox ID: sbx_abc123
4. Writes script to sandbox
5. Executes script in sandbox
6. Returns results
```

### Full-Stack Application Development

```
User: "Build a Vue + FastAPI todo app with sandbox isolation"
Sisyphus:
1. @\sisyphus-sandbox "Initialize sandbox for full-stack development"
2. Launches Librarian subagent (with sandbox context)
3. Launches Explore subagent (with sandbox context)
4. Builds app in sandbox
5. Tests in sandbox
6. Hosts frontend on port 5173
7. Returns public URL
```

### Parallel Subagent Execution

```
User: "Research authentication libraries and test them in isolation"
Sisyphus:
1. Initializes sandbox
2. Launches parallel subagents:
   - Librarian: Research auth libraries
   - Explore: Analyze existing code
   - Test agent: Test implementations in sandbox
3. Aggregates results
```

## Commands Reference

### Sisyphus-Specific Commands

| Command | Purpose | Example |
|----------|---------|---------|
| `\sisyphus-sandbox <prompt>` | Initialize sandbox for Sisyphus workflow | `\sisyphus-sandbox "Build and test API"` |
| `\subagent-sandbox <prompt> <sandbox_id>` | Execute subagent task in sandbox | `\subagent-sandbox "Run tests" sbx_abc123` |

### Direct Sandbox CLI Commands

```bash
# From .claude/skills/agent-sandboxes/sandbox_cli/

# Initialize sandbox
uv run sbx init --timeout 3600

# Execute command
uv run sbx exec <sandbox_id> "python test.py"

# Write file
uv run sbx files write <sandbox_id> /home/user/app.py "print('hello')"

# Read file
uv run sbx files read <sandbox_id> /home/user/app.py

# Host frontend
uv run sbx exec <sandbox_id> "npm run dev -- --port 5173" --background
uv run sbx sandbox get-host <sandbox_id> --port 5173
```

## Safety & Best Practices

### Always Use Sandboxes For:
- ✅ Running untrusted code
- ✅ Testing new packages
- ✅ Building applications
- ✅ Executing arbitrary commands
- ✅ Working with binary files

### NEVER Use Sandboxes For:
- ❌ File operations on local repository (git, commit, push)
- ❌ Documentation editing (local files)
- ❌ Configuration changes (local configs)

### Multi-Agent Safety
- Each agent should track its own sandbox ID
- Never use shell variables for sandbox IDs
- Always specify `--timeout` when initializing
- Use unique ports for browser testing (9222-9999)

## Troubleshooting

### "E2B_API_KEY not found"
```bash
# Add API key to .env
echo "E2B_API_KEY=your_key" >> .env
```

### "Sandbox timeout"
- Increase timeout: `sbx init --timeout 7200`
- Use `--timeout` on long exec commands

### "Sandbox not found"
- Check sandbox ID is correct
- Sandboxes auto-terminate after timeout
- Reinitialize if needed

### Browser automation fails
- Install Playwright: `uv pip install playwright && uv run playwright install chromium`
- Use `--port` flag for multi-agent safety
- Close browsers with `sbx browser close --port PORT`

## Cost Considerations

- E2B sandboxes: ~$0.05/hour
- GLM-4.7 tokens: $0.40/$1.50 per M
- Typical session: $0.10-0.30 (sandbox + model costs)
- Always stop sandboxes when done if not using auto-timeout
```

#### 4.2 Testing Checklist

**Unit Tests**:
- [ ] E2B_API_KEY validation works
- [ ] Sandbox initialization succeeds
- [ ] File upload/download works
- [ ] Command execution works
- [ ] Browser automation works

**Integration Tests**:
- [ ] Sisyphus can initialize sandbox
- [ ] Subagents receive sandbox context
- [ ] Parallel execution works
- [ ] Multi-agent safety (port isolation)
- [ ] Cleanup/destruction works

**User Workflows**:
- [ ] Ad-hoc sandbox operations
- [ ] Full-stack app build
- [ ] Parallel subagent tasks
- [ ] Browser testing
- [ ] Documentation generation

---

## Implementation Timeline

### Week 1: Core Integration (Phase 1)
- Day 1-2: Update SKILL.md for OpenCode
- Day 3-4: Create Sisyphus-specific prompts
- Day 5: Update prime command
- Day 6-7: Testing and validation

### Week 2: Enhancement (Phase 2-3)
- Day 1-3: Develop custom tool (optional)
- Day 4-5: Configure hooks
- Day 6-7: Integration testing

### Week 3: Documentation & Polish
- Day 1-3: Write comprehensive documentation
- Day 4-5: Create usage examples
- Day 6-7: Final testing and review

---

## Success Criteria

✅ **Functional Requirements**:
- Sisyphus automatically uses E2B sandboxes
- Subagents can execute code in isolation
- All existing workflows work with sandboxes
- No breaking changes to Claude Code compatibility

✅ **Non-Functional Requirements**:
- Safe execution (no code runs locally)
- Reliable sandbox lifecycle management
- Clear error messages and troubleshooting
- Documentation for all use cases

✅ **User Experience**:
- Transparent sandbox usage (automatic when needed)
- Easy to understand commands
- Minimal configuration required
- Works seamlessly with existing Sisyphus workflows

---

## Risks & Mitigation

### Risk 1: OpenCode Skills Compatibility
**Mitigation**: Use Claude-compatible skill structure (already supported)
**Fallback**: Custom tool approach if skills don't work

### Risk 2: Oh My OpenCode Hook Complexity
**Mitigation**: Use manual hook pattern via skill instructions
**Fallback**: Manual sandbox initialization in prompts

### Risk 3: Multi-Agent Sandbox Conflicts
**Mitigation**: Enforce unique sandbox IDs per agent, document pattern
**Fallback**: Template-based sandbox naming

### Risk 4: E2B Service Availability
**Mitigation**: Document fallback to local execution
**Fallback**: Use OpenCode's shell tool with warnings

---

## Next Steps

1. **Review Plan**: User reviews this plan and approves approach
2. **Implementation**: Execute Phases 1-3 in sequence
3. **Testing**: Comprehensive testing of all workflows
4. **Documentation**: Finalize user guides
5. **Deployment**: Merge to main branch and update README

---

## Appendix: Reference Materials

### Useful Links
- [OpenCode Skills Documentation](https://opencode.ai/docs/skills/)
- [Oh My OpenCode Configuration](https://ohmyopencode.com/configuration/)
- [E2B Documentation](https://e2b.dev/docs)
- [Agent Skills Standard](https://github.com/anthropics/agent-skills)

### Example Projects
- [agent-sandbox-skill](https://github.com/disler/agent-sandbox-skill/) - Original skill
- [oh-my-opencode](https://github.com/code-yeongyu/oh-my-opencode) - Plugin framework

### Key Files to Modify
- `.claude/skills/agent-sandboxes/SKILL.md` - Main skill definition
- `.claude/skills/agent-sandboxes/prompts/sisyphus-sandbox.md` - New prompt
- `.claude/commands/prime.md` - Update for OpenCode
- `.opencode/oh-my-opencode.json` - Configuration
- `docs/OPENCODE_E2B_USAGE.md` - User documentation

