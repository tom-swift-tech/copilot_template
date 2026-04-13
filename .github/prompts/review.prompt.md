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

## Test and Validate (mandatory before any approval)
> Reviewer does not run code, but Reviewer **does** verify that the implementer ran Test and Validate.
1. Confirm test evidence is present in the PR/handoff: test names, command output, or commit references.
2. Confirm new code paths have new tests. Untested new code is an automatic 🔴.
3. Confirm bug fixes have a regression test that demonstrably fails on the old code.
4. Confirm the test suite *as run by the implementer* matches the project's CI pipeline (no skipped suites, no `--no-verify`).
5. If evidence is missing, the review outcome is **Request Changes — provide test evidence**, not Approve.

## Output
- 🔴 **Blocking**: Must fix before merge
- 🟡 **Suggestion**: Should consider
- 🟢 **Nit**: Optional style preference
- ✅ **Good**: Positive pattern worth noting

Include something positive. End with: Approve / Request Changes / Needs Discussion.
