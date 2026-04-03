# GitHub Copilot Cheatsheet — Folder Agent V2

> Quick reference for the agent-based workflow. Pin this or keep it open.

---

## Agent Selection

Select agents from the **Copilot agent dropdown** in VS Code (or use `@agent-name` in chat):

| Agent        | When to Use                                    |
|-------------|------------------------------------------------|
| `Architect`  | Planning, design docs, ADRs — **can't edit code** |
| `Scaffolder` | New project setup, config, boilerplate — **no business logic** |
| `Builder`    | Day-to-day coding — **default, full access**   |
| `Reviewer`   | Code review, quality checks — **can't edit code** |

## Slash Commands

Type these in Copilot Chat:

| Command          | What It Does                              |
|------------------|-------------------------------------------|
| `/debug`         | Guided debugging session                  |
| `/feature`       | Full feature workflow (design → implement → test) |
| `/refactor`      | Safe refactoring with before/after tests  |
| `/review`        | Code review against project conventions    |
| `/test`          | Generate tests for selected code           |
| `/deploy`        | Deployment checklist and steps             |
| `/status`        | Current tasks and project state            |
| `/update-memory` | Save session learnings to memory files     |

## How It Works

1. **Always-on context**: `AGENTS.md` and `.github/copilot-instructions.md` load automatically.
2. **Scoped rules**: Language-specific instructions load only when editing matching files (Rust for `.rs`, Terraform for `.tf`, etc.).
3. **Agent handoffs**: Agents pass work to each other — Architect designs, Scaffolder structures, Builder implements, Reviewer gates.
4. **Persistent memory**: `.agent/memory/` stores gotchas, lessons, and patterns across sessions.

## Workflow Examples

### New Feature

1. Switch to **Architect** → describe what you need
2. Architect produces design doc → hands off to **Scaffolder**
3. Scaffolder creates structure → hands off to **Builder**
4. Builder implements → hands off to **Reviewer**
5. Reviewer approves or sends back to Builder

### Bug Fix

1. Use **Builder** (default) with `/debug`
2. After fix, `/test` to add regression test
3. `/review` for self-review before PR
4. `/update-memory` to capture the gotcha

### Quick Code Review

1. Switch to **Reviewer**
2. `/review` — gets conventions-aware feedback
3. Fix issues in **Builder** mode

## File Quick Reference

```
.github/
  copilot-instructions.md  ← Always-on (slim, points to .agent/)
  agents/                  ← Agent personas (dropdown selection)
  instructions/            ← Language rules (auto-load by file type)
  prompts/                 ← Slash commands
  skills/                  ← Multi-step capabilities

.agent/
  context/                 ← Project knowledge base
  tasks/                   ← Work tracking
  memory/                  ← Persistent learnings

AGENTS.md                  ← Agent definitions (always loaded)
CLAUDE.md                  ← Claude Code entry point
```

## Tips

- **Memory compounds**: Use `/update-memory` after every significant session. Future agents benefit.
- **Gotchas first**: Agents check `.agent/memory/gotchas.md` before implementing. Keep it current.
- **ADRs matter**: When Architect makes a design decision, it goes into `.agent/context/decisions.md`. Future agents won't re-debate it.
- **Scoped instructions are free**: They only load when relevant. Add more without bloating context.
