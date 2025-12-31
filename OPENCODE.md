# Engineering Rules for OpenCode

This file documents how to use the agent-sandboxes skill with OpenCode's custom command system.

## Custom Commands

OpenCode uses `/command-name` syntax (instead of Claude's `\command-name`). All commands are defined in `.opencode/command/` directory.

### General Commands

| Command | Description | Usage |
|---------|-------------|-------|
| `/prime` | Initialize project context and understand skills | `/prime` |
| `/generic-browser-test <url> <plan_file> [parallel] [headed]` | Browser UI testing | `/generic-browser-test https://example.com plan.md false true` |

### Agent Sandboxes Commands

| Command | Description | Usage |
|---------|-------------|-------|
| `/sandbox <prompt>` | Small general sandbox operations | `/sandbox run a Python script that prints hello world` |
| `/agent-sandboxes-plan-build-host-test <prompt> <workflow_id>` | Complete full-stack workflow | `/agent-sandboxes-plan-build-host-test "build a todo app" my-todo-app` |
| `/agent-sandboxes-plan-full-stack <prompt>` | Generate implementation plan | `/agent-sandboxes-plan-full-stack "notes app with CRUD"` |
| `/agent-sandboxes-build <plan_path>` | Execute a build plan | `/agent-sandboxes-build temp/workflow-123/specs/app.md` |
| `/agent-sandboxes-host <sandbox_id> <port>` | Expose port and get public URL | `/agent-sandboxes-host sbx_abc123 5173` |
| `/agent-sandboxes-test <sandbox_id> <url> <plan_path> <workflow_id>` | Run validation tests | `/agent-sandboxes-test sbx_abc123 https://5173-sbx_abc123.e2b.app temp/specs/app.md workflow-123` |
| `/agent-sandboxes-browser-testing <sandbox_id> <url> <plan_path> <workflow_id>` | Browser UI testing | `/agent-sandboxes-browser-testing sbx_abc123 https://5173-sbx_abc123.e2b.app temp/specs/app.md workflow-123` |

## How Commands Work

Each command instructs the agent to:
1. Read the skill documentation at `.claude/skills/agent-sandboxes/SKILL.md`
2. Read the corresponding prompt file from `.claude/skills/agent-sandboxes/prompts/`
3. Execute the workflow with the provided arguments

This approach works with any OpenCode agent configuration, including oh-my-opencode.

## Command Mapping

| OpenCode Command | Prompt File |
|------------------|-------------|
| `/sandbox` | `prompts/sandbox.md` |
| `/agent-sandboxes-plan-build-host-test` | `prompts/plan-build-host-test.md` |
| `/agent-sandboxes-plan-full-stack` | `prompts/plan-full-stack.md` |
| `/agent-sandboxes-build` | `prompts/build.md` |
| `/agent-sandboxes-host` | `prompts/host.md` |
| `/agent-sandboxes-test` | `prompts/test.md` |
| `/agent-sandboxes-browser-testing` | `prompts/browser-testing.md` |

## Key Files

- **Skill Documentation**: `.claude/skills/agent-sandboxes/SKILL.md` - Main reference for sandbox operations
- **Sandbox CLI**: `.claude/skills/agent-sandboxes/sandbox_cli/` - Python CLI for E2B sandbox operations
- **Workflow Prompts**: `.claude/skills/agent-sandboxes/prompts/` - Detailed workflow instructions

## Configuration

See `opencode.json` in the project root for OpenCode-specific configuration including:
- Tool permissions (bash, edit, etc.)
- Permission settings
