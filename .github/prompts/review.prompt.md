---
description: 'Code review — conventions, architecture, tests, security, performance, IaC safety'
agent: 'reviewer'
tools: ['search/codebase', 'search/usages']
---

# Code Review Workflow

Review the specified code or recent changes. **Do NOT make edits.**

Check against:
1. [.agent/context/conventions.md](../../.agent/context/conventions.md) — code style
2. [.agent/context/decisions.md](../../.agent/context/decisions.md) — architecture alignment
3. [.agent/context/infrastructure.md](../../.agent/context/infrastructure.md) — IaC standards (if applicable)
4. [.agent/memory/gotchas.md](../../.agent/memory/gotchas.md) — known pitfalls

## Checklist
- Correctness, edge cases, error handling
- Conventions and naming consistency
- Test coverage for new code paths
- Security: input validation, secrets out of code, auth enforcement
- IaC: no secrets in state, destroy-safe, tags present, idempotent
- Performance: N+1 queries, missing caching, memory leaks

## Output
- 🔴 **Blocking**: Must fix before merge
- 🟡 **Suggestion**: Should consider
- 🟢 **Nit**: Optional style preference
- ✅ **Good**: Positive pattern worth noting

Include something positive. End with: Approve / Request Changes / Needs Discussion.
