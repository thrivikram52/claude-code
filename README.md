# Claude Code Knowledge Base

A personal knowledge base for Claude Code — tips, workflows, tools, and ready-to-use configuration.

---

## Knowledge Files

| File | What's Inside |
|------|--------------|
| [**claude-code-guide.md**](claude-code-guide.md) | Core concepts, tips & tricks, commands, SDK reference, subagent & hook recipes |
| [**coding-practices.md**](coding-practices.md) | Explore-Plan-Execute workflow, prompt engineering, TDD, eval patterns, multi-agent strategies |
| [**awesome-tools.md**](awesome-tools.md) | Curated list of AI dev tools, UI design tools, and productivity resources |

---

## Ready-to-Use Config

The `.claude/` directory contains real, working configuration you can copy into your own projects:

```
.claude/
├── agents/          # 9 subagents (Architect, QA, Security, PM, etc.)
├── commands/        # 7 slash commands (primer, fix-github-issue, etc.)
└── hooks/           # Hook scripts and configuration
```

### Quick Start

```bash
# 1. Clone and cd into the repo
git clone <this-repo> && cd claude-code

# 2. Start Claude Code
claude

# 3. Prime the repo context
/primer

# 4. Copy agents/commands to your own project
cp -r .claude/agents/ /path/to/your-project/.claude/agents/
cp -r .claude/commands/ /path/to/your-project/.claude/commands/
```

---

## Resources

- [Claude Code Documentation](https://docs.anthropic.com/en/docs/claude-code)
- [Claude Code Best Practices](https://www.anthropic.com/engineering/claude-code-best-practices)
- [MCP Server Library](https://github.com/modelcontextprotocol)
- [Claude Marketplace](https://claudemarketplaces.com/)
