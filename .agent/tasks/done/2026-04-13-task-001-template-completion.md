# TASK-001: Complete the template (drift prevention, kickoff doc, standards expansion, mandatory Test & Validate)

- **Completed**: 2026-04-13
- **Branch**: `main`
- **Status**: Done

## Summary

Closed the remaining ~5% of the Folder Agent template by:

1. **Test and Validate baked into every workflow.** All 6 work-producing prompts (`feature`, `debug`, `refactor`, `test`, `deploy`, `review`) end with a mandatory `Test and Validate` section. `update-memory` also gained one (memory writes are work). `status` is intentionally exempt (read-only).
2. **Agent gates.** Builder requires test evidence in handoff. Scaffolder must produce compiling structure with failing placeholder tests. Reviewer rejects work without test evidence (automatic Request Changes). Architect now requires a `Test Plan` section in design docs and a `Validation` section in ADRs.
3. **Drift prevention.** Added `.github/skills/sync-agents/SKILL.md` — verifies the agent roster across `.github/agents/`, `AGENTS.md`, and `copilot-instructions.md`. Refuses to auto-fix (drift = intent change). Includes its own Test and Validate procedure (positive control + negative control).
4. **Day 1 kickoff.** Added `docs/GETTING-STARTED.md` with an 8-step runbook. Final step is a smoke test that includes verifying the agent boundaries actually load (asks Builder to design something — must refuse).
5. **Standards expansion.** Added `DATABASE-DESIGN.md`, `OBSERVABILITY.md`, `DEPLOYMENT.md`, `SECURITY.md` to `standards/`. Each ends with Test and Validate. Updated `standards/README.md` to index them.
6. **Entry-point sync.** README architecture diagram, customization steps, and standards table updated. CLAUDE.md state line records the changes.

## Acceptance Criteria

- [x] All 6 workflow prompts (excluding `status` and `update-memory`) end with a `Test and Validate` section
- [x] `update-memory` also gained Test and Validate (during execution we decided memory writes count as work)
- [x] Builder and Scaffolder agents require test evidence before handoff
- [x] Reviewer agent rejects work without test evidence
- [x] Design doc template includes a `Test Plan` section
- [x] ADR template includes a `Validation` section
- [x] `.github/skills/sync-agents/SKILL.md` exists and documents the drift check
- [x] `docs/GETTING-STARTED.md` walks through Day 1 setup
- [x] `standards/` contains DATABASE-DESIGN.md, OBSERVABILITY.md, DEPLOYMENT.md, SECURITY.md
- [x] `standards/README.md` indexes all standards
- [x] README.md, CLAUDE.md reflect the new files
- [x] Test and Validate gate: agent roster sync check 🟢, all cross-references resolve

## Test and Validate Evidence

**sync-agents drift check (manual run on 2026-04-13):**

```
Canonical agents (from .github/agents/): architect, builder, reviewer, scaffolder

AGENTS.md table         → 4 rows: Architect, Scaffolder, Builder, Reviewer ✓
copilot-instructions.md → 4 rows: Architect, Scaffolder, Builder, Reviewer ✓
Org chart in AGENTS.md  → "Architect → Scaffolder → Builder → Reviewer → merge" ✓

Field consistency:
- Architect: Opus 4.6 / GPT-5.2 / "No — design docs only"        ✓ all 3 sources match
- Scaffolder: Codex GPT-5.3 / Sonnet 4.6 / "Yes — structure only" ✓ all 3 sources match
- Builder: Codex GPT-5.3 / Sonnet 4.6 / "Yes — full access"      ✓ all 3 sources match
- Reviewer: Opus 4.6 / Sonnet 4.6 / "No — feedback only"          ✓ all 3 sources match

🟢 sync-agents: OK — 4 agents in sync
```

**Test and Validate coverage check:**

- Prompts: `feature`, `debug`, `refactor`, `test`, `deploy`, `review`, `update-memory` — all have a Test and Validate section. `status` is intentionally exempt (read-only).
- Agents: `builder`, `scaffolder`, `reviewer` have explicit Test and Validate gates. `architect` enforces it via the required Test Plan section in the design doc template (architect writes no code).
- Templates: `docs/adr/TEMPLATE.md` has Validation section. `docs/design/TEMPLATE.md` has Test Plan section.
- Standards: All 5 standards files end with a Test and Validate section.
- Entry points: README.md and CLAUDE.md reference the rule.

**Cross-reference smoke test:** Spot-checked GETTING-STARTED.md links — all referenced files exist.

## Notes

- Meta-validation: this task used the template's own `.agent/tasks/current.md` format to track itself, validating that the format actually holds together for real work.
- The sync-agents skill deliberately refuses to auto-fix. Drift usually means *someone changed something on purpose* and forgot to propagate; auto-fix would silently overwrite intent.
- One typo caught and fixed during execution: SECURITY.md auth-bypass test had a duplicated phrase. Caught by re-reading; would have been caught again by markdown lint if we had one.
