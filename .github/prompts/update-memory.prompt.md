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

## Test and Validate (mandatory — even memory updates must be validated)

> Memory rot is silent. An update that wrote to the wrong file or duplicated an existing entry corrupts every future read. Validate before claiming done.

1. **Re-read each touched memory file** and confirm the new entries are present, dated, and in the right section.
2. **Duplicate check**: grep the file for the new entry's distinguishing phrase. If you see it twice, you appended to a file that already had it — remove the duplicate.
3. **Classification check**: a "lesson" in `gotchas.md` is a category error. Re-read the entry and confirm it's in the right file (lesson vs gotcha vs pattern).
4. **Format check**: each entry has a date and follows the format documented in the file's header. No malformed entries.
5. **If a task was moved to `done/`**: confirm the file is gone from `current.md` and present in `done/` with the date prefix.

## Output

Summarize what was added to memory after making the changes, and confirm the Test and Validate checks above passed.
