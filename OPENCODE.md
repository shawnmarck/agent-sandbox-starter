# Engineering Rules for OpenCode

OpenCode reads the agent-sandboxes skill from `.claude/skills/` automatically. This file documents how to use the skill with OpenCode's custom command system.

## Custom Commands

OpenCode uses `/command-name` syntax (instead of Claude's `\command-name`). All commands are defined in `.opencode/command/` directory.

### Available Commands

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

Each command:
1. Uses the `use_skill` tool to load the `agent-sandboxes` skill from `.claude/skills/agent-sandboxes/SKILL.md`
2. Reads the corresponding prompt file from `.claude/skills/agent-sandboxes/prompts/`
3. Executes the workflow with the provided arguments

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

## Skill Discovery

OpenCode automatically discovers skills in these locations:
- `.opencode/skill/<name>/SKILL.md` (project-specific)
- `~/.opencode/skill/<name>/SKILL.md` (global)
- `.claude/skills/<name>/SKILL.md` (Claude-compatible)

The agent-sandboxes skill is located at `.claude/skills/agent-sandboxes/SKILL.md` and is automatically discovered by OpenCode.

## Configuration

See `opencode.json` in the project root for OpenCode-specific configuration including:
- Tool permissions (bash, skill, etc.)
- Permission settings

