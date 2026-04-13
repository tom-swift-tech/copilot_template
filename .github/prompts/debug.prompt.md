---
description: 'Structured debugging workflow — check gotchas, reproduce, isolate, fix, verify'
agent: 'builder'
tools: ['search/codebase', 'terminal/runCommand', 'search/usages']
---

# Debugging Workflow

## Step 1: Gather Context
1. Read [.agent/memory/gotchas.md](../../.agent/memory/gotchas.md) — might be a known issue.
2. Read [.agent/context/project.md](../../.agent/context/project.md) for architecture.
3. Search the codebase for the relevant code path.

## Step 2: Reproduce
1. Create or identify a failing test that demonstrates the bug.
2. If you cannot reproduce, ask for more details before proceeding.

## Step 3: Isolate
1. Binary search the call chain — where does data go wrong?
2. Check recent changes: `git log --oneline -10 -- <file>`
3. Check if an upstream dependency changed behavior.

## Step 4: Fix
1. Implement the minimal fix addressing the root cause.
2. Follow conventions in [.agent/context/conventions.md](../../.agent/context/conventions.md).

## Step 5: Verify
1. Write a regression test.
2. Run the project's test suite — confirm no regressions.
3. Run the linter.

## Step 6: Update Memory
- [.agent/memory/gotchas.md](../../.agent/memory/gotchas.md) — if root cause was non-obvious
- [.agent/memory/lessons.md](../../.agent/memory/lessons.md) — what you learned
