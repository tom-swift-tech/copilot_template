# Project Instructions

<!-- Auto-loaded by Copilot on every chat request. Keep this slim — point to CLAUDE.md and .agent/ for details. -->

## Project Context

**For full project context, read `CLAUDE.md`.** It contains purpose, architecture, conventions, and current state.

For non-Copilot runtimes (Claude Code, Cursor, Codex), CLAUDE.md is the canonical context file.

## Project Knowledge Base

This repository uses a structured knowledge base in `.agent/`. Always consult it:

- **Architecture & stack**: `.agent/context/project.md`
- **Code conventions**: `.agent/context/conventions.md`
- **Design decisions**: `.agent/context/decisions.md`
- **Infrastructure conventions**: `.agent/context/infrastructure.md`
- **Known gotchas**: `.agent/memory/gotchas.md`
- **Current task**: `.agent/tasks/current.md`
- **Discovered patterns**: `.agent/memory/patterns.md`

## Operating Modes

This project uses five agent roles. Select from the agent dropdown in chat, or use the corresponding slash command. See `AGENTS.md` for the full roster, model routing, and handoff protocol.

| Agent | Model | Can edit code? |
|-------|-------|---------------|
| **Architect** | Claude Opus 4.6 / GPT-5.2 | No — design docs only |
| **Analyst** | Claude Opus 4.6 / GPT-5.2 | No — critique only, never proposals |
| **Scaffolder** | Codex GPT-5.3 / Claude Sonnet 4.6 | Yes — structure only |
| **Builder** | Codex GPT-5.3 / Claude Sonnet 4.6 | Yes — full access (default) |
| **Reviewer** | Claude Opus 4.6 / Claude Sonnet 4.6 | No — feedback only |

## Prime Directives

1. **Read before you write.** Check `CLAUDE.md`, `.agent/context/`, and `.agent/memory/gotchas.md` before changes.
2. **Follow conventions.** Match patterns in `.agent/context/conventions.md` and `.agent/context/infrastructure.md`.
3. **Verify before done.** Run test + lint commands before marking work complete.
4. **Update memory.** Use `/update-memory` to capture learnings after sessions.
5. **Never commit secrets.** Use Key Vault refs, env vars, or `.env.example` with placeholders.

## Code Style Defaults

- Prefer explicit over clever — readable code wins
- Include error handling in all generated code — no happy-path-only
- Follow existing patterns in the codebase over general best practices when they conflict
- Enterprise-aware: consider RBAC, audit logging, and compliance implications
- Infrastructure-aware: consider blast radius and rollback strategy for IaC changes
