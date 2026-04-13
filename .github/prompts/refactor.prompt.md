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

## Step 4: Test and Validate (mandatory — do not skip)
> A refactor is not done until Test and Validate passes against the **same test suite** that passed at baseline.
1. Run the full test suite — **all** the same tests that passed in Step 1 must still pass. Zero new failures, zero deletions.
2. Run the linter and type checker — clean.
3. Run any benchmarks that motivated the refactor (perf claims need numbers, not vibes).
4. Diff-check: was anything tested before the refactor that is now untested? If so, restore coverage before claiming done.
5. Capture before/after evidence (test counts, lint output, benchmark deltas) for the Reviewer.

## Step 5: Document
1. Update [.agent/context/decisions.md](../../.agent/context/decisions.md) if architecture changed.
2. Update [.agent/memory/patterns.md](../../.agent/memory/patterns.md) with new patterns.
