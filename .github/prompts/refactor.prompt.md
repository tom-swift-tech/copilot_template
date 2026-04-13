---
description: 'Safe refactoring — baseline tests, plan blast radius, incremental changes, verify'
agent: 'builder'
tools: ['search/codebase', 'terminal/runCommand', 'search/usages']
---

# Refactoring Workflow

## Ground Rules
- Must not change behavior. Same tests pass before and after.
- One refactor at a time. Never mix with features or fixes.

## Step 1: Baseline
1. Run full test suite — ALL must pass before starting.
2. Read [.agent/context/decisions.md](../../.agent/context/decisions.md) — there may be a reason it looks like that.

## Step 2: Plan
1. What specifically is being improved? (readability, DRY, type safety, performance)
2. Map blast radius with #tool:search/usages.

## Step 3: Execute Incrementally
1. Small changes, verify tests after each step.
2. If tests break, revert last change and reconsider.

## Step 4: Verify & Document
1. Full test suite + linter pass.
2. Update [.agent/context/decisions.md](../../.agent/context/decisions.md) if architecture changed.
3. Update [.agent/memory/patterns.md](../../.agent/memory/patterns.md) with new patterns.
