---
mode: agent
description: "Capture lessons, gotchas, and patterns from the current session into .agent/memory/"
tools:
  - editFiles
  - readFiles
---

# Update Memory

Review the current conversation and extract any reusable knowledge into the appropriate memory files.

## Process

1. Read the current session context (recent changes, errors encountered, solutions found).
2. Classify each piece of knowledge:
   - **Lesson**: A general takeaway that changes how we approach future work → `.agent/memory/lessons.md`
   - **Gotcha**: A specific trap or surprising behavior that will recur → `.agent/memory/gotchas.md`
   - **Pattern**: A reusable solution or code structure worth repeating → `.agent/memory/patterns.md`
3. Check for duplicates — don't add what's already captured.
4. Append new entries with today's date.
5. If a task was completed, move it from `.agent/tasks/current.md` to `.agent/tasks/done/` with a date prefix.

## Format Guidelines

- **Lessons**: Short actionable statements with context and impact.
- **Gotchas**: One-liner problem → fix format, bracketed category, dated.
- **Patterns**: Named pattern with when/how/example/date.

## Output

Summarize what was added to memory after making the changes.
