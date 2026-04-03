# CLAUDE.md

> Claude Code auto-loads this file. It bridges the Folder Agent pattern to Claude Code's conventions.

## Project Type

Enterprise IaC template with the Folder Agent pattern — the folder structure IS the agent.

## Architecture

Two-layer design:

- **`.github/`** — GitHub Copilot-native primitives (agents, instructions, prompts, skills)
- **`.agent/`** — LLM-agnostic knowledge base (context, tasks, memory)

Both layers are active. When using Claude Code, read from both.

## Context Loading Order

1. This file (always loaded first by Claude Code)
2. `AGENTS.md` — agent personas and handoff protocol
3. `.agent/context/project.md` — project-specific details
4. `.agent/context/conventions.md` — coding standards
5. `.agent/context/infrastructure.md` — enterprise IaC conventions
6. `.agent/context/stack.md` — dependencies and toolchain
7. `.agent/context/decisions.md` — architecture decision records
8. `.agent/tasks/current.md` — active work items
9. `.agent/memory/gotchas.md` — known pitfalls (check before implementing)

## Operating Mode

Default to **Builder** mode (full implementation access). If the task is clearly:

- **Design/planning**: Switch to Architect mode (read-only, produce docs)
- **Scaffolding**: Switch to Scaffolder mode (structure and config only)
- **Review**: Switch to Reviewer mode (read-only, produce feedback)

Announce mode switches explicitly.

## Language Conventions

Language-specific rules are in `.github/instructions/`. Load the relevant file based on the language you're working in:

- Rust: `.github/instructions/rust.instructions.md`
- TypeScript: `.github/instructions/typescript.instructions.md`
- Python: `.github/instructions/python.instructions.md`
- Terraform: `.github/instructions/terraform.instructions.md`
- Ansible: `.github/instructions/ansible.instructions.md`
- PowerShell: `.github/instructions/powershell.instructions.md`
- Docs: `.github/instructions/docs.instructions.md`

## Memory

After completing significant work, update memory:

- `.agent/memory/lessons.md` — general takeaways
- `.agent/memory/gotchas.md` — specific traps to remember
- `.agent/memory/patterns.md` — reusable solutions

## Task Tracking

- Current work: `.agent/tasks/current.md`
- Backlog: `.agent/tasks/backlog.md`
- Completed: `.agent/tasks/done/`
