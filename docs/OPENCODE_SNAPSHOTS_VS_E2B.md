# OpenCode Snapshots vs E2B Sandboxes: Architecture Comparison

## Critical Question: What Will We Use?

**Your Question**: Will this use opencode/glm in snapshots in opencode equivalent of "dangerously permissive mode for claude"?

**Answer**: **NO** - The proposed integration uses **E2B sandboxes**, NOT OpenCode snapshots.

---

## Architecture Options

### Option A: E2B Sandboxes (Recommended)

**How It Works**:
```
User Request
    ↓
Sisyphus (GLM-4.7)
    ↓
Initialize E2B Sandbox
    ↓
All code execution in sandbox
    ↓
Results returned to user
```

**Isolation Level**: **Complete**
- Code runs in cloud E2B environment
- Zero access to local filesystem
- Zero access to local network
- Zero access to local processes
- Commercial-grade security

**OpenCode Mode**: Normal/default (no special permissions needed)

**Cost**: ~$0.05/hour for E2B sandboxes

**Pros**:
- ✅ Maximum security/isolation
- ✅ Works with any OpenCode configuration
- ✅ No permission changes needed
- ✅ Commercial-grade isolation
- ✅ Automatic cleanup (timeout)
- ✅ Multi-agent safety (unique IDs)

**Cons**:
- ❌ Requires E2B API key
- ❌ Slight latency (cloud execution)
- ❌ Costs money (but minimal)

---

### Option B: OpenCode Snapshots

**How It Works**:
```
User Request
    ↓
Sisyphus (GLM-4.7)
    ↓
Create OpenCode Snapshot
    ↓
Code runs in snapshot
    ↓
Results returned to user
```

**Isolation Level**: **Partial**
- Code runs in OpenCode's managed environment
- Limited access to local filesystem
- Still some process isolation
- Depends on OpenCode's security model

**OpenCode Mode**: Would require "dangerously permissive" equivalent
- Need to find OpenCode's permission configuration
- May require `permission: { "bash": "allow" }` or similar
- Less security by design

**Cost**: Free (local execution)

**Pros**:
- ✅ Free (no E2B costs)
- ✅ Lower latency (local execution)
- ✅ No external dependencies

**Cons**:
- ❌ Lower security isolation
- ❌ Requires permission configuration changes
- ❌ May need "permissive mode" equivalent
- ❌ Risk of accessing local resources
- ❌ Less control over cleanup
- ❌ Multi-agent safety more complex

---

## What Claude's "Dangerously Permissive Mode" Means

In Claude Code:
- **Normal Mode**: Tools require approval for certain operations
- **Dangerously Permissive Mode**: All tools auto-approved, no confirmation prompts
- **Purpose**: Faster workflow, less interruption
- **Risk**: Agent can execute any command without confirmation

## OpenCode's Equivalent

OpenCode uses **permission-based system** (from docs found):

```json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "bash": "allow",        // or "deny" or "ask"
    "edit": "allow",
    "webfetch": "ask"
  }
}
```

**OpenCode Permission Modes**:
- `allow`: Tool always allowed (like permissive mode)
- `deny`: Tool never allowed
- `ask`: Tool requires approval

**OpenCode does NOT have "dangerously permissive"** mode specifically, but `permission: { "bash": "allow" }` achieves similar effect.

---

## Key Differences

| Aspect | E2B Sandboxes | OpenCode Snapshots |
|---------|----------------|-------------------|
| **Isolation** | Complete (cloud) | Partial (local) |
| **Security** | Commercial-grade | Depends on OS/permissions |
| **Permissions Needed** | None | May need `bash: "allow"` |
| **Cost** | ~$0.05/hour | Free |
| **Latency** | ~50-100ms | ~5-20ms |
| **Multi-Agent Safety** | Easy (unique IDs) | Complex (shared env) |
| **Cleanup** | Automatic (timeout) | Manual |
| **External Access** | None | Local filesystem access |

---

## My Recommendation: Use E2B Sandboxes

### Why E2B Is Better for Your Requirements

**Requirement**: "Make sure we can do that safely and successfully"

**E2B Sandboxes**:
- ✅ Complete isolation from local system
- ✅ No risk of malware spreading to main PC
- ✅ Zero chance of accessing sensitive local files
- ✅ Commercial-grade security (used by enterprise)
- ✅ Automatic cleanup - no orphaned processes
- ✅ Easy multi-agent safety (unique sandbox IDs)

**OpenCode Snapshots**:
- ⚠️ Still runs on your main PC
- ⚠️ Could potentially access local files (if permissions allow)
- ⚠️ Risk if GLM-4.7 misinterprets and runs dangerous command
- ⚠️ Harder to ensure multi-agent isolation
- ⚠️ Manual cleanup required

### Cost vs. Safety Tradeoff

**E2B Cost**: ~$0.05/hour
- 1 hour of work = $0.05
- Full day of development = $0.40
- **Small price for maximum security**

**OpenCode Cost**: $0
- Free, but you're trading security for cost
- If malware runs, it runs on YOUR main PC
- If GLM-4.7 misinterprets, it affects YOUR system

**My Recommendation**: The $0.05/hour is worth the security.

---

## Hybrid Approach (Optional)

If you want BOTH benefits:

### Option C: OpenCode Snapshots for Safe Tasks, E2B for Dangerous Tasks

```javascript
// Conceptual logic in skill
if (isSafeTask(task)) {
  useOpenCodeSnapshot()
} else {
  useE2BSandbox()
}
```

**Safe Tasks** (OpenCode snapshots):
- Reading local files
- Editing documentation
- Running `git status`, `git diff`
- Analyzing existing code

**Dangerous Tasks** (E2B sandboxes):
- Installing new packages
- Running arbitrary code
- Testing unknown scripts
- Building applications
- Executing shell commands

**Pros**:
- Cost optimization (E2B only when needed)
- Maximum security when needed
- Fast local execution for safe tasks

**Cons**:
- More complex logic
- Need to define "safe" vs "dangerous"
- Still requires E2B API key

---

## Configuration Examples

### For E2B Sandboxes (Recommended)

```json
// ~/.config/opencode/opencode.json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "bash": "ask",      // Ask for approval (safer)
    "edit": "allow",    // Allow editing
    "webfetch": "ask"
  }
  // No special "permissive mode" needed
}
```

**Use**: 
- Sisyphus automatically uses E2B sandboxes via skill
- No permission changes needed
- Normal security mode maintained

### For OpenCode Snapshots

```json
// ~/.config/opencode/opencode.json
{
  "$schema": "https://opencode.ai/config.json",
  "permission": {
    "bash": "allow",      // Allow all bash commands (permissive)
    "edit": "allow",
    "webfetch": "allow"
  }
  // This is OpenCode's "permissive mode" equivalent
}
```

**Use**:
- Code runs in OpenCode's snapshot environment
- No E2B sandbox integration
- Less security, free execution

---

## Updated Plan Based on Your Question

### If You Want E2B Sandboxes (Recommended)

**Implementation**: Use existing plan in `docs/OPENCODE_E2B_INTEGRATION_PLAN.md`
- No changes needed
- OpenCode stays in normal/secure mode
- E2B provides isolation

### If You Want OpenCode Snapshots

**Implementation Needed**:
1. Research OpenCode snapshot API
2. Remove E2B skill integration
3. Configure OpenCode permissions to `bash: "allow"`
4. Create snapshot management prompts
5. Test security implications
6. Document "permissive mode" risks

**My Recommendation**: Don't do this unless you have a specific reason to avoid E2B.

---

## Decision Matrix

Choose based on your priorities:

| Priority | Choose | Why |
|-----------|---------|-----|
| **Maximum Security** | E2B Sandboxes | Complete isolation, commercial-grade |
| **Cost** | OpenCode Snapshots | Free execution |
| **Safety** | E2B Sandboxes | No risk to local system |
| **Latency** | OpenCode Snapshots | Faster local execution |
| **Multi-Agent** | E2B Sandboxes | Easy isolation, unique IDs |
| **Compliance** | E2B Sandboxes | Easier for enterprise/production |

---

## My Recommendation Summary

**For Your Use Case** (OpenCode + GLM-4.7 + safe execution):

**Use E2B Sandboxes**

Why:
1. ✅ Maximum security (no local code execution)
2. ✅ Meets your requirement: "make sure we can do that safely"
3. ✅ Works seamlessly with Sisyphus
4. ✅ Easy multi-agent isolation
5. ✅ Reasonable cost ($0.05/hour)
6. ✅ No permission configuration changes needed
7. ✅ Automatic cleanup
8. ✅ Commercial-grade isolation

**Do NOT Use OpenCode Snapshots** for this use case because:
1. ❌ Less secure (still runs locally)
2. ❌ Requires "permissive" permissions (like dangerously permissive mode)
3. ❌ Risk if GLM-4.7 makes mistakes
4. ❌ Harder multi-agent isolation
5. ❌ Manual cleanup

**Cost is minimal** compared to security benefit.

---

## Next Steps

**If E2B Sandboxes (Recommended)**:
1. Review existing plan: `docs/OPENCODE_E2B_INTEGRATION_PLAN.md`
2. No changes needed - plan already uses E2B
3. Say "approve E2B approach" to proceed

**If OpenCode Snapshots**:
1. Tell me you prefer OpenCode snapshots
2. I'll revise the plan to remove E2B
3. I'll research OpenCode snapshot API deeply
4. I'll add permission configuration guidance
5. I'll document security risks of "permissive mode"

**If Hybrid Approach**:
1. Tell me you want both
2. I'll define safe vs dangerous task classification
3. I'll add logic to use E2B only when needed
4. Optimize for cost while maintaining security

---

## Bottom Line

**To answer your question directly**:

**NO**, this will NOT use OpenCode snapshots in "permissive mode". 

It will use **E2B sandboxes** for complete isolation, keeping OpenCode in normal/secure mode.

**E2B provides the security** instead of relying on OpenCode's permission system.

The $0.05/hour cost is worth the commercial-grade isolation for safe agentic execution.
