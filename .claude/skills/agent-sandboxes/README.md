# Agent Sandbox Skill

An agent skill for managing isolated execution environments using [E2B Sandboxes](https://e2b.dev/). 
This skill enables AI agents (Gemini CLI, Claude Code, Codex CLI) to safely execute code, build full-stack applications, and perform arbitrary engineering tasks in a secure, isolated sandbox.


## Why Use Agent Sandboxes? The **Value Proposition**
> 
> Agent Sandboxes unlock 3 key capabilities for your agentic engineering:

- **Isolation**: Each agent fork runs in a fully isolated, gated E2B sandbox, this means no matter what your agent does, it's secure and safe from your local filesystem and production environment.
- **Scale**: You can run as many agent forks as you want, each fork is independent and has its own sandbox. This is a very literal way to scale your compute to scale your impact.
- **Agency**: Your agents have full control over the sandbox environment, they can install packages, modify files, run commands, etc. This means they can handle more of the engineering process for you.


## 🚀 Features

*   **Isolated Execution**: Run untrusted code, tests, and binaries safely.
*   **Full-Stack Development**: Scaffold, build, and host Vue + FastAPI + SQLite apps.
*   **Browser Automation**: Built-in Playwright integration for visual validation.
*   **Agent-First Design**: Optimized for CLI agents with structured prompts and robust error handling.
*   **Persistent Context**: Tools to manage sandbox lifecycles across agent turns.

## 🛠️ Setup

### Prerequisites

*   Python >= 3.12
*   `uv` package manager (recommended)
*   E2B Account & API Key

### Installation

1.  **Clone the repository** (if not already done).
2.  **Configure Environment**:
    Create a `.env` file in the project root:
    ```bash
    cp .env.sample .env
    ```
    Add your E2B API key:
    ```env
    E2B_API_KEY=sbx_...
    ```
    *(Get your key from [E2B Dashboard](https://e2b.dev/dashboard/keys))*

3.  **Open your Agentic Coding Tool**:
    Open your Agentic Coding Tool and start prompting!

    ```bash
    claude
    or
    gemini
    or
    codex
    ```

    Then for one of simple tasks run:

    ```bash
    \sandbox <prompt>
    ```

    For complex tasks run
    ```bash
    \plan-build-host-test <prompt> <workflow_id>
    ```


## 🤖 "Reprogrammed" BACKSLASH Commands

This project uses "reprogrammed" backslash commands to trigger agent workflows.
See [CLAUDE.md](CLAUDE.md), [AGENTS.md](AGENTS.md), and [GEMINI.md](GEMINI.md) for more details.

| Command                                                                         | Description                                                                                  |
| :------------------------------------------------------------------------------ | :------------------------------------------------------------------------------------------- |
| `\sandbox <prompt>`                                                             | Small general sandbox operations. An adhoc prompt with minimal compute usage.                |
| `\agent-sandboxes:plan-full-stack <prompt>`                                     | Generates a detailed implementation plan with Browser UI Testing workflows.                  |
| `\agent-sandboxes:build <plan_path>`                                            | Executes a build plan within a sandbox.                                                      |
| `\agent-sandboxes:host <sandbox_id> <port>`                                     | Exposes a port and returns a public URL.                                                     |
| `\agent-sandboxes:test <sandbox_id> <url> <plan_path> <workflow_id>`            | Runs validation tests against the hosted app including browser UI testing.                   |
| `\agent-sandboxes:browser-testing <sandbox_id> <url> <plan_path> <workflow_id>` | Executes browser UI testing workflows from a plan using parallel subagents (headed default). |
| `\agent-sandboxes:plan-build-host-test <prompt> <workflow_id>`                  | **Agentic-Workflow**: Orchestrates a full lifecycle: Plan → Build → Host → Test.             |

## 💻 CLI Usage (Manual)

You can also run the CLI manually for debugging or inspection:

```bash
# From .claude/skills/agent-sandboxes/sandbox_cli/
uv run sbx --help
```

### Common Commands

*   **Init**: `uv run sbx init --timeout 1800`
*   **Execute**: `uv run sbx exec <sandbox_id> "echo hello"`
*   **Files**: `uv run sbx files ls <sandbox_id> /home/user`
*   **Browser**: `uv run sbx browser start`

## 🏗️ Architecture

*   **CLI**: Python-based (`click`, `e2b`, `rich`) located in `.claude/skills/agent-sandboxes/sandbox_cli/`.
*   **Prompts**: Markdown-based prompt templates in `.claude/skills/agent-sandboxes/prompts/`.
*   **Skill Definition**: `SKILL.md` defines the capabilities exposed to the agent.

## 📄 Documentation

*   [Skill Definition & Guide](.claude/skills/agent-sandboxes/SKILL.md)
*   [CLI README](.claude/skills/agent-sandboxes/sandbox_cli/README.md)
*   [E2B Documentation](https://e2b.dev/docs)