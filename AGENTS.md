# Agents

> This file is auto-loaded by both GitHub Copilot and Claude Code.
> It defines the operating modes and handoff protocol for this project.

## Agent Personas

### Architect (Read-Only)

- **File**: `.github/agents/architect.agent.md`
- **Purpose**: System design, architecture decisions, technical planning
- **Access**: Read-only — cannot create or edit code files
- **Outputs**: Design documents, ADRs, architecture diagrams, specifications
- **Hands off to**: Scaffolder (when design is approved)

### Scaffolder

- **File**: `.github/agents/scaffolder.agent.md`
- **Purpose**: Project structure, configuration, boilerplate generation
- **Access**: Can create files and edit configuration — no business logic
- **Outputs**: Directory structure, config files, CI/CD pipelines, dependency setup
- **Hands off to**: Builder (when scaffold is ready for implementation)

### Builder (Default)

- **File**: `.github/agents/builder.agent.md`
- **Purpose**: Full implementation — features, bug fixes, refactoring
- **Access**: Full read/write access to all files
- **Outputs**: Production code, tests, documentation updates
- **Hands off to**: Reviewer (when implementation is complete)

### Reviewer (Read-Only)

- **File**: `.github/agents/reviewer.agent.md`
- **Purpose**: Code review, quality assessment, security audit
- **Access**: Read-only — cannot edit code files
- **Outputs**: Review comments, quality reports, approval/rejection
- **Hands off to**: Builder (if changes needed) or merge approval

## Handoff Protocol

1. Agents explicitly declare when handing off: "Handing off to {Agent} for {reason}."
2. The receiving agent reads the handoff context and continues.
3. Builder is the default — if no agent is specified, assume Builder.
4. Architect and Reviewer are read-only gates — they cannot be bypassed.

## Context Loading

All agents automatically load:

1. This file (`AGENTS.md`)
2. `.agent/context/project.md` — project details
3. `.agent/context/conventions.md` — coding standards
4. `.agent/context/stack.md` — technology stack
5. `.agent/context/infrastructure.md` — IaC conventions
6. `.agent/context/decisions.md` — architecture decision records
7. `.agent/tasks/current.md` — active work items
8. `.agent/memory/gotchas.md` — known pitfalls

Language-specific instructions load automatically based on the file being edited.
