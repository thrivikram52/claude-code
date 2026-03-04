# CLAUDE.md

This file provides guidance to Claude Code when working in this repository.

## Repo Structure

This is a personal knowledge base for Claude Code. Key files:

- `claude-code-guide.md` — Claude Code concepts, tips, commands, SDK reference
- `coding-practices.md` — General AI-assisted coding workflows and prompt engineering
- `awesome-tools.md` — Curated tools and repos
- `.claude/` — Working Claude Code config (agents, commands, hooks)
- `archive/` — Old source files preserved for reference

## Core Development Philosophy

### KISS (Keep It Simple, Stupid)

Simplicity should be a key goal in design. Choose straightforward solutions over complex ones whenever possible.

### YAGNI (You Aren't Gonna Need It)

Avoid building functionality on speculation. Implement features only when they are needed.

### Design Principles

- **Dependency Inversion**: High-level modules should not depend on low-level modules. Both should depend on abstractions.
- **Open/Closed Principle**: Software entities should be open for extension but closed for modification.
- **Single Responsibility**: Each function, class, and module should have one clear purpose.
- **Fail Fast**: Check for potential errors early and raise exceptions immediately when issues occur.

## Code Structure & Modularity

### File and Function Limits

- **Never create a file longer than 500 lines of code**. If approaching this limit, refactor by splitting into modules.
- **Functions should be under 50 lines** with a single, clear responsibility.
- **Classes should be under 100 lines** and represent a single concept or entity.
- **Organize code into clearly separated modules**, grouped by feature or responsibility.
- **Line length should be max 100 characters**.
