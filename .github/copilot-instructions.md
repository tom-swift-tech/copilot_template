# Project Instructions

<!-- Auto-loaded by Copilot on every chat request. Keep this slim — point to .agent/ for details. -->

## Project Knowledge Base

This repository uses a structured knowledge base in `.agent/`. Always consult it:

- **Architecture & stack**: [.agent/context/project.md](.agent/context/project.md)
- **Code conventions**: [.agent/context/conventions.md](.agent/context/conventions.md)
- **Design decisions**: [.agent/context/decisions.md](.agent/context/decisions.md)
- **Infrastructure conventions**: [.agent/context/infrastructure.md](.agent/context/infrastructure.md)
- **Known gotchas**: [.agent/memory/gotchas.md](.agent/memory/gotchas.md)
- **Current task**: [.agent/tasks/current.md](.agent/tasks/current.md)
- **Discovered patterns**: [.agent/memory/patterns.md](.agent/memory/patterns.md)

## Operating Modes

This project uses four agent personas. Select from the agent dropdown in chat, or use the corresponding slash command.

| Agent | Purpose | Can Edit Code? |
|-------|---------|---------------|
| **Architect** | System design, ADRs, trade-off analysis | No — design docs only |
| **Scaffolder** | Project structure, boilerplate, config | Yes — structure only |
| **Builder** | Implementation, features, bug fixes (default) | Yes — full access |
| **Reviewer** | Code review, quality checks | No — feedback only |

## Prime Directives

1. **Read before you write.** Check `.agent/context/` and `.agent/memory/gotchas.md` before changes.
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
