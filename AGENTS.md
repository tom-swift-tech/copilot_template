# Agents

> This file is auto-loaded by both GitHub Copilot and Claude Code.
> It defines the functional agent org, model routing, and handoff protocol.

## Org Structure

```
Human (project owner)
  │  Directives, approvals, merge decisions
  ▼
Architect ──► Scaffolder ──► Builder ──► Reviewer ──► merge
  (design)     (structure)    (code)      (validate)
```

Each agent has a defined role with strict boundaries. No agent does two jobs. Reviewer never reviews its own work.

## Agent Roster

| Agent | Role | Model (preferred) | Model (fallback) | Can edit code? |
|-------|------|--------------------|-------------------|---------------|
| **Architect** | System design, ADRs, trade-offs | Claude Opus 4.6 | GPT-5.2 | No — design docs only |
| **Scaffolder** | Project structure, boilerplate, config | Codex GPT-5.3 | Claude Sonnet 4.6 | Yes — structure only |
| **Builder** | Implementation, features, bug fixes | Codex GPT-5.3 | Claude Sonnet 4.6 | Yes — full access |
| **Reviewer** | Code review, quality, security | Claude Opus 4.6 | Claude Sonnet 4.6 | No — feedback only |

## Model Routing Rationale

- **Architect** needs frontier reasoning for decomposition, trade-off analysis, and system design. Opus or GPT-5.2.
- **Scaffolder** and **Builder** are code gen workhorses. Optimized for speed and volume. Codex GPT-5.3 or Sonnet.
- **Reviewer** needs careful reading and judgment. Opus for critical-path reviews (Tier 1), Sonnet for routine reviews (Tier 2-4).

## Agent Files

| Agent | Definition |
|-------|-----------|
| Architect | `.github/agents/architect.agent.md` |
| Scaffolder | `.github/agents/scaffolder.agent.md` |
| Builder | `.github/agents/builder.agent.md` |
| Reviewer | `.github/agents/reviewer.agent.md` |

## Handoff Protocol

1. Agents explicitly declare when handing off: "Handing off to {Agent} for {reason}."
2. The receiving agent reads the handoff context and continues.
3. Builder is the default — if no agent is specified, assume Builder.
4. Architect and Reviewer are read-only gates — they cannot be bypassed.

Standard flow: **Architect → Scaffolder → Builder → Reviewer → merge**

Not every task needs every agent. A bug fix skips Architect and Scaffolder. A design discussion stays in Architect. Match the flow to the work.

## Context Loading

All agents automatically load:

1. This file (`AGENTS.md`)
2. `CLAUDE.md` — project context for Claude Code (purpose, architecture, conventions, state)
3. `.agent/context/project.md` — project details
4. `.agent/context/conventions.md` — coding standards
5. `.agent/context/stack.md` — technology stack
6. `.agent/context/infrastructure.md` — IaC conventions
7. `.agent/context/decisions.md` — architecture decision records
8. `.agent/tasks/current.md` — active work items
9. `.agent/memory/gotchas.md` — known pitfalls

Language-specific instructions load automatically based on the file being edited (`.github/instructions/`).

## Org Rules

1. **No agent does two jobs.** Architect designs. Builder builds. Reviewer reviews.
2. **Reviewer never reviews its own work.** Always a different agent from the implementer.
3. **Architect never writes code.** If it's producing implementation, the role boundary is broken.
4. **Builder never makes architectural decisions.** Flag to Architect, don't decide.
5. **Stubs should be minimal.** No speculative exports. Add code when the first consumer needs it.
6. **Read before you write.** Every agent checks `.agent/context/` and `.agent/memory/gotchas.md` before acting.
