---
description: 'New feature implementation — read spec, check conventions, implement, test, update memory'
agent: 'builder'
tools: ['search/codebase', 'terminal/runCommand', 'search/usages', 'vscode/askQuestions']
---

# New Feature Workflow

## Step 1: Understand
1. Read [.agent/tasks/current.md](../../.agent/tasks/current.md) for acceptance criteria.
2. Read design doc (if one exists in `docs/design/`).
3. Read [.agent/context/conventions.md](../../.agent/context/conventions.md).
4. Use #tool:vscode/askQuestions if requirement is unclear.

## Step 2: Check Prior Art
1. Search codebase for similar functionality.
2. Check [.agent/memory/patterns.md](../../.agent/memory/patterns.md) for established patterns.

## Step 3: Implement
1. Interface first — types and signatures before logic.
2. Core logic inside-out.
3. Tests alongside, not after.

## Step 4: Verify
1. Run test suite — all pass.
2. Run linter — clean.
3. All acceptance criteria from `tasks/current.md` met.

## Step 5: Update Memory
- Update [.agent/memory/lessons.md](../../.agent/memory/lessons.md)
- Update [.agent/memory/patterns.md](../../.agent/memory/patterns.md) if new pattern established
- Move task to `.agent/tasks/done/`
