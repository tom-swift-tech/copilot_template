---
name: sync-agents
description: "Verify the agent roster is consistent across .github/agents/, AGENTS.md, and .github/copilot-instructions.md — and refuse to silently fix drift"
---

# Sync Agents Skill

The agent roster lives in three places that must stay in lock-step:

1. **`.github/agents/*.agent.md`** — the canonical agent definitions (one file per agent)
2. **`AGENTS.md`** — the roster table, model routing, handoff org chart, and org rules
3. **`.github/copilot-instructions.md`** — the "Operating Modes" table

If they drift, agents reading one source will believe a different reality than agents reading another. This skill detects drift and reports it. **It does not auto-fix** — a human must decide which source is correct, because drift usually means a real intent change that needs review.

## When to Run

- After adding, removing, or renaming an agent
- After changing an agent's model routing (preferred or fallback)
- After changing an agent's edit permissions ("Can edit code?")
- Before tagging a template release
- As part of pre-merge review on any PR that touches `.github/agents/`, `AGENTS.md`, or `.github/copilot-instructions.md`

## What "Consistent" Means

For every agent that exists in any of the three sources, **all three** must agree on:

| Field | Source of truth | Where it must also appear |
|-------|-----------------|---------------------------|
| Agent name | `.github/agents/<name>.agent.md` (front-matter `name`) | AGENTS.md row, copilot-instructions.md row |
| Description | Agent file front-matter `description` | AGENTS.md "Role" column |
| Preferred model | Agent file front-matter `model[0]` | AGENTS.md "Model (preferred)", copilot-instructions.md "Model" |
| Fallback model | Agent file front-matter `model[1]` (if present) | AGENTS.md "Model (fallback)" |
| Edit capability | Agent file presence/absence of `tools: codebase/editFiles` | AGENTS.md "Can edit code?", copilot-instructions.md "Can edit code?" |
| Handoffs | Agent file `handoffs:` block | AGENTS.md handoff protocol diagram |

The org-chart diagram in `AGENTS.md` (`Architect → Scaffolder → Builder → Reviewer → merge`) must include exactly the agents that exist as files.

## Steps

1. **Enumerate canonical agents**: list every `.github/agents/*.agent.md` file. This is the source of truth set.

2. **Parse front-matter** from each agent file: `name`, `description`, `model`, `tools`, `handoffs`.

3. **Parse the AGENTS.md roster table** under "Agent Roster". Extract one record per row.

4. **Parse the copilot-instructions.md "Operating Modes" table**. Extract one record per row.

5. **Diff the three sets**:
   - **Missing**: agents in `.github/agents/` but not in either table
   - **Orphan**: rows in either table with no matching agent file
   - **Mismatch**: same agent, different value for a field listed in the table above

6. **Parse the AGENTS.md org-chart code block**. Confirm it lists exactly the agents in the file set, in the documented order.

7. **Report**:
   - 🟢 If everything matches: print `sync-agents: OK — N agents in sync` and exit
   - 🔴 If drift is found: print a structured report grouped by `Missing | Orphan | Mismatch | OrgChart`, listing the file, field, and conflicting values for each finding. Do not modify any file.

## Output Format

```
sync-agents report
─────────────────────────────────────────
Canonical agents (from .github/agents/): architect, builder, reviewer, scaffolder

🔴 Mismatch: builder.preferred_model
   .github/agents/builder.agent.md  → "Codex GPT-5.3"
   AGENTS.md                        → "Claude Sonnet 4.6"
   .github/copilot-instructions.md  → "Codex GPT-5.3"

🔴 Orphan: AGENTS.md row "Coordinator" has no matching .github/agents/coordinator.agent.md

🟢 Org chart matches file set
```

## Why This Skill Does Not Auto-Fix

Drift usually means *someone changed something on purpose* and forgot to propagate. Auto-fixing would silently overwrite the intended change. The human reviewer should:

1. Decide which source reflects the actual intent.
2. Hand-edit the other two to match.
3. Re-run `sync-agents` to confirm green.

Auto-fix is a footgun for a structure where the canonical source is *the agent's behavior*, not just its name.

## Test and Validate (mandatory — this skill validates the template, so it must itself be validated)

> A drift-detection skill that gives false-greens is worse than no skill at all. After invoking this skill, the operator must verify the check actually fired.

1. **Positive control**: confirm the report includes the count of canonical agents found. If the number is `0`, the file scan failed — investigate before trusting any green result.
2. **Negative control on first run**: deliberately introduce a one-character drift in `AGENTS.md` (e.g., change `Builder` to `Builders`), re-run, and confirm the skill reports a 🔴. Revert the test edit. This proves the check actually compares values rather than just claiming success.
3. **Coverage check**: confirm every agent file in `.github/agents/` appears in the report's "Canonical agents" line. A missing file = a broken file scan.
4. **Re-read after fix**: after any drift is corrected by hand, re-run the skill and confirm a clean 🟢 report before claiming the fix is complete.
5. Capture the final 🟢 report in the PR or handoff message as evidence.

## Invocation

- **From an agent**: invoke this skill by name when working on agent definitions or before merging changes to `.github/agents/`, `AGENTS.md`, or `copilot-instructions.md`.
- **From a slash command**: future `/sync` prompt can wrap this skill.
- **From CI**: a thin wrapper script can parse the same files and exit non-zero on drift. Not shipped with the template — projects that want CI enforcement add it themselves.
