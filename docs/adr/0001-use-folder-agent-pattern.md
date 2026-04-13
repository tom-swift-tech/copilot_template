# ADR 0001: Use the Folder Agent pattern

- **Status:** Accepted
- **Date:** 2026-04-12
- **Deciders:** Project owner

## Context

We need a way to ship AI-assisted development conventions to teams that works across multiple AI coding tools (GitHub Copilot, Claude Code, Codex, Cursor) without maintaining tool-specific scaffolding per project. The conventions change per project (Azure vs AWS, Terraform vs Bicep, Rust vs TypeScript) but the *mechanism* for exposing them to agents should be uniform.

## Decision

Adopt the **Folder Agent** pattern: encode agent behavior in the folder structure itself via two layers.

- `.github/` — GitHub Copilot-native primitives (agents, instructions, prompts, skills). Auto-discovered by Copilot.
- `.agent/` — LLM-agnostic knowledge base (context, tasks, memory). Readable by any agent.

Top-level entry points (`CLAUDE.md`, `AGENTS.md`, `README.md`) cross-reference both layers so every supported tool lands on the same conventions.

## Consequences

**Positive:**
- New projects adopt the template with a single copy-paste; no framework install.
- Tool-switching is free — the same context files work for any LLM.
- Conventions version with the code (in git), not in a separate tooling repo.

**Negative:**
- Some content is referenced from multiple locations (agent roster in `AGENTS.md`, `CLAUDE.md`, `copilot-instructions.md`). Schema drift is a real risk and must be watched during review.
- The pattern relies on agents actually reading context files. Teams that skip the read-before-write discipline get no benefit.

## Alternatives Considered

1. **Tool-specific templates per AI product.** Rejected — explodes maintenance burden and couples teams to a single vendor.
2. **External framework (e.g., LangGraph, CrewAI) for agent orchestration.** Rejected — adds runtime dependency and steep learning curve for a problem solvable with markdown.
3. **Monolithic CONVENTIONS.md.** Rejected — context bloat; every agent invocation pays for content it doesn't need.
