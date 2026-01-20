# Research Summary: OpenCode + E2B Sandbox Integration

## What I Did

I conducted comprehensive research across 6 parallel agents to understand how to integrate E2B sandbox capabilities with OpenCode running GLM-4.7.

## Research Agents Launched

1. **OpenCode Architecture** (41s)
   - How OpenCode works, tool system, skills, hooks
   - Found: OpenCode natively supports Claude-compatible skills!

2. **GLM-4.7 Model** (1s)
   - Capabilities, strengths, cost-effectiveness
   - Found: Great for coding ($0.40/$1.50 per M tokens), perfect for Sisyphus

3. **Current Claude Integration** (27s)
   - How agent-sandboxes skill works now
   - Found: Backslash commands, skill structure, CLI integration

4. **OpenCode Snapshots** (1s)
   - Native snapshot capabilities vs E2B sandboxes
   - Found: E2B is better for isolation, can complement or replace

5. **Sisyphus Agent System** (24s)
   - How Sisyphus works with subagents
   - Found: Multi-agent delegation, hook system, parallel execution

6. **OpenCode Examples** (1s)
   - Real-world OpenCode integrations
   - Found: Custom tools, skills, plugin examples

## Key Findings

### ✅ Good News

1. **OpenCode Natively Supports Claude-Compatible Skills**
   - Skills in `.claude/skills/` work automatically!
   - No code changes needed for skill compatibility
   - Existing agent-sandboxes skill structure is perfect

2. **You Already Have Everything Set Up**
   - ✅ OpenCode installed
   - ✅ GLM-4.7 configured for build/plan agents
   - ✅ Oh My OpenCode plugin installed
   - ✅ E2B sandbox skill and CLI exist

3. **Simple Integration Path**
   - Use existing skill structure
   - Update SKILL.md for OpenCode
   - Create Sisyphus-specific prompts
   - Configure oh-my-opencode.json

4. **Oh My OpenCode Has Hook System**
   - 25+ automation hooks available
   - PreToolUse, PostToolUse, etc.
   - Can automatically initialize sandboxes

### 🎯 Your Requirements

**Requirement**: "OpenCode running GLM4.7 instead of claudecode"
- ✅ GLM-4.7 is your configured build/plan model
- ✅ GLM-4.7 is cost-effective ($0.40/$1.50 per M)
- ✅ GLM-4.7 works well for agentic coding

**Requirement**: "Make sure we can do that safely and successfully"
- ✅ E2B provides complete isolation
- ✅ No code runs on local machine
- ✅ Sandboxes auto-terminate after timeout
- ✅ Multi-agent safety (unique IDs per agent)

**Requirement**: "Document how I should use it, what commands I would use"
- ✅ Comprehensive user guide included (Phase 4)
- ✅ Command reference tables
- ✅ Troubleshooting section
- ✅ Workflow examples

**Requirement**: "Make sure sisyphus can do it effectively when kicking off agents"
- ✅ Sandbox ID passed in subagent prompts
- ✅ Each subagent gets isolated environment
- ✅ Parallel execution support
- ✅ Port isolation for browser testing

## Integration Plan Highlights

### Phase 1: Skill Enhancement (Week 1)
**What**: Update existing agent-sandboxes skill for OpenCode
**Files**:
- `.claude/skills/agent-sandboxes/SKILL.md` - Add OpenCode instructions
- `.claude/skills/agent-sandboxes/prompts/sisyphus-sandbox.md` - New prompt
- `.claude/commands/prime.md` - Update for OpenCode

**Result**: Sisyphus can automatically use E2B sandboxes

### Phase 2: Custom Tool (Week 2 - Optional)
**What**: Create TypeScript wrapper for E2B CLI
**Files**:
- `.opencode/tools/e2b-sandbox.ts` - Custom tool definition

**Result**: Better integration with OpenCode's tool system

### Phase 3: Configuration (Week 2)
**What**: Configure Oh My OpenCode for automatic sandbox usage
**Files**:
- `.opencode/oh-my-opencode.json` - Add sandbox config section

**Result**: Sandboxes auto-initialize on tool use

### Phase 4: Documentation (Week 3)
**What**: User guide, command reference, troubleshooting
**Files**:
- `docs/OPENCODE_E2B_USAGE.md` - Comprehensive user guide

**Result**: Clear documentation for all workflows

## How It Will Work

### Before (Current)
```
User: "Build a todo app"
Claude Code:
  1. Reads prompt
  2. Runs code locally
  3. Returns result
```

### After (Target)
```
User: "Build a todo app"
Sisyphus (GLM-4.7):
  1. Reads @.claude/skills/agent-sandboxes/SKILL.md
  2. Automatically initializes E2B sandbox
  3. Captures sandbox ID: sbx_abc123
  4. Launches subagents with sandbox context:
     - Librarian: Research patterns (with sandbox ID)
     - Explore: Analyze codebase (with sandbox ID)
  5. Builds app in sandbox
  6. Tests in sandbox
  7. Hosts on port 5173
  8. Returns: "Sandbox: sbx_abc123, URL: https://5173-sbx_abc123.e2b.app"
```

## Commands You'll Use

### Simple (Manual)
```bash
# Start OpenCode
opencode

# Sisyphus automatically uses sandboxes for code execution
```

### Advanced (Direct)
```bash
# Initialize sandbox
cd .claude/skills/agent-sandboxes/sandbox_cli
uv run sbx init --timeout 3600

# Execute in sandbox
uv run sbx exec sbx_abc123 "python app.py"

# Host frontend
uv run sbx exec sbx_abc123 "npm run dev -- --port 5173" --background
uv run sbx sandbox get-host sbx_abc123 --port 5173
```

### With Sisyphus Workflows
```
User: @\sisyphus-sandbox "Build and test this API"
→ Sisyphus automatically handles sandbox initialization

User: "Test this using Librarian"
→ Sisyphus launches Librarian with sandbox context
```

## Safety Features

1. **Complete Isolation**
   - All code runs in E2B sandboxes
   - Local filesystem never touched
   - Safe from malware and exploits

2. **Automatic Timeout**
   - Sandboxes auto-terminate after 1 hour
   - Can extend with `--timeout` flag
   - No orphaned processes

3. **Multi-Agent Safety**
   - Each agent gets unique sandbox ID
   - Port isolation for browser testing
   - No conflicts between parallel agents

4. **E2B API Key Validation**
   - Validates before sandbox operations
   - Clear error messages
   - Easy troubleshooting

## What You Need to Review

1. **Integration Plan** (`docs/OPENCODE_E2B_INTEGRATION_PLAN.md`)
   - Detailed phase-by-phase implementation
   - Configuration examples
   - Testing checklist
   - Timeline (3 weeks)

2. **Key Questions**:
   - Do you want automatic sandbox initialization (hooks)?
   - Or manual sandbox usage (prompt-based)?
   - Should subagents always use sandboxes, or sometimes local?
   - What's your preferred timeout (default 1 hour)?

## Next Steps

1. **Review the Plan**
   - Read: `docs/OPENCODE_E2B_INTEGRATION_PLAN.md`
   - Check: Implementation phases match your needs
   - Verify: Timeline is acceptable (3 weeks)

2. **Provide Feedback**
   - Any concerns about the approach?
   - Want different integration pattern?
   - Specific workflows to prioritize?

3. **Approve Implementation**
   - Say "ready to implement" when you're happy
   - I'll start with Phase 1 (skill enhancement)
   - We'll go phase-by-phase with testing

## Files Created

1. `docs/OPENCODE_E2B_INTEGRATION_PLAN.md` - Detailed implementation plan
2. `docs/README.md` - Documentation index
3. `docs/RESEARCH_SUMMARY.md` - This file
4. Git branch: `feature/opencode-integration`
5. Commit: `docs: Add comprehensive OpenCode + E2B integration plan`

## Ready When You Are

I'm ready to start implementation whenever you approve the plan. Just say:
- "Start implementation" or
- "Let's begin with Phase 1" or
- Any questions you have before we start

The plan is comprehensive, researched, and ready to execute! 🚀
