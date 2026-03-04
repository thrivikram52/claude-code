# Claude Code Guide

Everything about Claude Code — concepts, tips, commands, SDK, and configuration.

---

## Concepts

### 1. Prompts

The natural language instructions you give Claude. Power keywords like `think`, `think hard`, `think harder`, and `ultrathink` map to increasing levels of thinking budget.

### 2. Slash Commands

Frequently used prompts saved as reusable commands (e.g., `/security-scan`, `/primer`). Stored as markdown files in `.claude/commands/`. Type `/` to see all available commands, including MCP prompts (`/mcp__servername__promptname`).

### 3. Tools

Built-in capabilities Claude can use — Bash commands, git commands, file operations, web search, etc. Configure which tools are allowed via permissions.

### 4. MCP (Model Context Protocol)

A protocol for connecting Claude to external services. MCP servers provide additional tools, resources, and prompts. Manage with `claude mcp add|list|get|remove`.

### 5. Skills

Reusable instruction sets (stored as markdown files) that extend Claude's capabilities with specialized knowledge and workflows.

```
pdf/
├── SKILL.md              # Main instructions (loaded when triggered)
├── FORMS.md              # Form-filling guide (loaded as needed)
├── reference.md          # API reference (loaded as needed)
├── examples.md           # Usage examples (loaded as needed)
└── scripts/
    ├── analyze_form.py   # Utility script (executed, not loaded)
    ├── fill_form.py      # Form filling script
    └── validate.py       # Validation script
```

**Key detail:** The description in `SKILL.md` is loaded at the system prompt level — it's part of every message. When a skill includes scripts, the SKILL.md should instruct Claude to **execute them using bash_tool**, not just read them.

Install skills directly: `npx skills add https://github.com/vercel-labs/agent-skills --skill vercel-react-best-practices`

### 6. Agents

An agent = Prompt + Tools + MCP + Skills. The primary Claude Code instance is itself an agent.

### 7. Subagents

Subagents run in their **own context window** with their own Prompt + Tools + MCP + Skills. The main agent delegates tasks to subagents (up to 4 concurrently). Each subagent receives a single prompt — they don't have the full conversation history.

**Subagent = Prompt + Tools + Permissions + Model**

### 8. Hooks

Shell commands that execute at predefined lifecycle events:

| Event | When it runs |
|-------|-------------|
| `PreToolUse` | Before tool execution (can block operations) |
| `PostToolUse` | After successful tool completion |
| `UserPromptSubmit` | When user submits a prompt |
| `SubagentStop` | When a subagent completes |
| `Stop` | When the main agent finishes responding |
| `SessionStart` | At session initialization |
| `PreCompact` | Before context compaction |
| `Notification` | During system notifications |

Use `/hookify` to create hooks via natural conversation without writing JSON manually.

### 9. Plugins

Custom collections of slash commands, agents, MCP servers, skills, and hooks that install with a single command.

Example: A security plugin might contain a **security-auditor subagent**, **MCP servers** to connect to vulnerability databases, and custom **slash commands** for scanning. Plugins are the **packaging system**; agents are **components** inside a plugin.

Plugins can be shared, published to the marketplace, and installed by teams.

### 10. Marketplace

Where you can browse, publish, and install plugins. Community marketplace: [claudemarketplaces.com](https://claudemarketplaces.com/)

### 11. Agent SDK

Run Claude Code programmatically. See the [SDK section](#sdk) below for details.

### 12. Scopes

5 scopes, from highest to lowest precedence:

| Scope | Access | Storage |
|-------|--------|---------|
| **Enterprise** | All users on machine | System-level `managed-settings.json` |
| **Command line** | Current session only | CLI arguments (temporary) |
| **Local** | You, this repo only | `.claude/*.local.*` (not in git) |
| **Project** | All collaborators | `.claude/` (committed to git) |
| **User** | You, all projects | `~/.claude/` directory |

If a permission is allowed in user settings but denied in project settings, the project setting wins.

### 13. LSP (Language Server Protocol)

Used by tools like Serena for semantic code understanding — go-to-definition, find references, etc.

### 14. Async Operations

Subagents can run in the background. Long-running jobs don't block the main conversation.

### 15. Claude Code on Web/Mobile/Slack

Connect via GitHub Apps. Code runs on Anthropic's cloud and pushes directly to GitHub.

### 16. Sandbox

Controlled bash environment — no need to approve each request. Cannot download from unauthorized domains or contact unallowed domains. Operations outside the sandbox are blocked at the OS level.

---

## Tips & Tricks

### Session Management

- **Name sessions** with `/rename session-name` so you can resume with `/resume session-name`
- **Clear context** with `/clear` when switching between unrelated tasks
- **Double escape and resume** — one of the most powerful workflow optimizations for managing context and creating branching workflows
- Run **5 Claude instances in parallel** across numbered terminal tabs. Use system notifications to know when one needs input. Simple tasks go to the web/mobile app.

### Plan Mode Workflow

- Most sessions start in **Plan mode** (`Shift+Tab` twice)
- Go back and forth with Claude until you like the plan
- Then switch to **auto-accept edits mode** — Claude can usually one-shot it from a good plan
- A good plan is really important!

### Context & Configuration

- Push `CLAUDE.md` to git so teams can share it
- Use `CLAUDE.md` in subfolders for module-specific context (monorepo pattern)
- **Single CLAUDE.md** for simple projects; **rules folder** with modular `.md` files for complex ones
- Use `CHANGELOG.md` for tracking all changes
- Use a `Primer.md` for loading entire context at once
- Split large specs/stories across multiple files

### Productivity

- Use **`ultrathink`** for extended thinking on complex problems
- Keep frequently used slash commands and subagents ready
- Give Claude access to all the tools and MCPs it needs
- **Long-running jobs** should run in background
- Use `PostToolUse` hooks to auto-format code — Claude handles 90%, hooks handle the last 10%
- **Use Git worktrees** for parallel coding sessions in separate folders
- The most important thing for great results: **give Claude a way to verify its work**. That feedback loop 2-3x the quality.

### Self-Improving Workflows

- Create a `/reflect` skill to summarize the current session and improve the skills used (e.g., code review). Instead of you updating skills manually, Claude updates and pushes to git.
- Create a `/learn` command to run mid-session after solving something non-trivial. It extracts the pattern, drafts a skill file, and asks for confirmation before saving.

### Using Opus

Opus with thinking is the best coding model. Even though it's bigger and slower than Sonnet, you steer it less and it's better at tool use — so it's almost always faster in the end.

### GitHub Automation

- Automate GitHub workflows and use `@claude` during code review on the GitHub tab
- Use `/fix-github-issue 123` to auto-fetch, analyze, fix, test, and create a PR

---

## Commands & Shortcuts

### CLI Commands

| Command | Description |
|---------|-------------|
| `claude` | Start interactive session |
| `claude -p "prompt"` | Headless mode (no interactive UI) |
| `claude -p "prompt" --output-format json` | Headless mode with JSON output |
| `claude --resume abc123` | Resume a session |
| `claude --resume abc123 --fork-session` | Resume with a forked session |
| `claude --system-prompt "..."` | Override system prompt |
| `claude --append-system-prompt "..."` | Append to system prompt |
| `claude --chrome` | Launch with Chrome integration |
| `claude --dangerously-skip-permissions` | YOLO mode (skip all permission prompts) |

### Keyboard Shortcuts

| Shortcut | Action |
|----------|--------|
| `Shift+Tab` | Toggle plan mode |
| `Ctrl+0` | View Claude's thinking verbose |
| `ESC` | Interrupt Claude |
| `/clear` | Clear context window |
| `/init` | Generate initial CLAUDE.md |
| `/settings` | Open Claude settings |
| `/permissions` | Manage tool permissions |
| `/agents` | Manage subagents |

### Permission Setup

```json
{
  "allowedTools": [
    "Edit", "Read", "Write",
    "Bash(git add:*)",
    "Bash(git commit:*)",
    "Bash(npm:*)",
    "Bash(python:*)",
    "Bash(pytest:*)"
  ]
}
```

Save in `.claude/settings.local.json` for project-level permissions.

---

## SDK

The Claude Code SDK lets you run Claude programmatically. Key capabilities:

### Memory Tool
Store and retrieve information outside the context window.

### Prompt Caching
Reduces cost and latency when the input prompt is identical across multiple calls.

### Context Editing
Manage the context window by removing older messages, thinking blocks, or summarizing at the client side to compact conversations.

### Extended Thinking
Enhanced reasoning for complex tasks. Use with `"thinking"` parameter.

### Batch Processing
For non-real-time workloads where cost matters. Send batch requests, poll results after ~1 hour.

### Structured Output
Define an output schema; Claude follows it in responses.

### Tools & Tool Runner
**Tool calling** = Claude calls your defined functions and gets results back.
**Tool runner** = out-of-the-box solution that automatically handles tool calls, results, and conversation management.

### Fine-Grained Tool Streaming
Get partial results of tool execution via streams.

### Tool Search Tool
Dynamically discover and load tools on-demand instead of loading all definitions upfront. Solves context bloat from tool definitions.

### Bash Tool
You maintain the bash process. Claude sends commands, you execute them and return stdout/stderr. Unlike Claude Code where terminal sessions are managed for you.

### Code Execution Tool
A managed, sandboxed environment hosted by Anthropic. Unlike the bash tool, Anthropic runs everything for you.

### Code Execution + MCP
Agents can write code to interact with MCP servers, load only needed tools, and process data in the execution environment before passing results back to the model.

### Programmatic Tool Calling
Claude writes code that calls tools programmatically within a code execution container, rather than requiring round trips through the model for each invocation. Solves large intermediate results polluting context.

### Computer Tool

```
┌─────────────────────┐
│  YOUR COMPUTER      │
│  script.py running  │ ──API call──> ┌──────────────┐
│                     │               │ CLAUDE API   │
└─────────────────────┘               └──────────────┘
         ↓ HTTP                              ↑
┌─────────────────────┐                      │
│  DOCKER CONTAINER   │                      │
│  ┌───────────────┐  │ ──sends screenshots──┘
│  │ Tool Executor │  │
│  └───────────────┘  │ ←─receives tool requests
│         ↓           │
│  X11 Server (Xvfb)  │
│         ↓           │
│  Virtual Desktop    │
│  (Firefox, Apps)    │
└─────────────────────┘
```

### Progressive Disclosure
Read tool definitions on-demand rather than loading them all upfront.

### Tool Use Examples
Provide example API usage on the tool definition to prevent parameter errors. E.g., specify date formats explicitly instead of letting Claude guess.

---

## Subagent Recipes

This repo includes pre-built subagents in `.claude/agents/`:

| Agent | Purpose |
|-------|---------|
| `Architect.md` | System design and architecture |
| `BackEndDev.md` | Backend implementation |
| `FrontEndDev.md` | Frontend implementation |
| `UIDesigner.md` | UI/UX design |
| `ProductManager.md` | Product requirements |
| `QA.md` | Testing and quality assurance |
| `Security.md` | Security auditing |
| `Devops.md` | DevOps and deployment |
| `DocumentationManager.md` | Auto-update docs on code changes |

### Creating Subagents

```markdown
---
name: security-auditor
description: "Security specialist. Proactively reviews code for vulnerabilities."
tools: Read, Grep, Glob
---

You are a security auditing specialist...
```

**Best practices:**
- One clear specialty per subagent
- Use "proactively" in descriptions for automatic invocation
- Only give tools they actually need (`Read, Grep` for review-only agents)
- Design for one-shot context — they get a single prompt, not conversation history

---

## Hook Recipes

### Auto-Format Code on Edit

```json
{
  "hooks": {
    "PostToolUse": [{
      "matcher": ".*",
      "hooks": [{
        "type": "command",
        "command": ".claude/hooks/format-code.sh"
      }]
    }]
  }
}
```

### Tool Usage Logging

The repo includes `.claude/hooks/log-tool-usage.sh` — logs all tool usage with timestamps to `.claude/logs/tool-usage.log`.

---

## MCP Server Setup

### Serena (Semantic Code Analysis)

```bash
claude mcp add serena -- uvx --from git+https://github.com/oraios/serena \
  serena start-mcp-server --context ide-assistant --project $(pwd)
```

Provides: semantic code retrieval, LSP-powered editing, support for Python/TypeScript/Go/Rust/Java/C++.

### Managing MCP Servers

```bash
claude mcp list          # List all configured servers
claude mcp get serena    # Get details
claude mcp remove serena # Remove a server
```
