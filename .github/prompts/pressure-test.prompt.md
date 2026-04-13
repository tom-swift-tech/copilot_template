---
description: 'Pressure-test a design doc or ADR — adversarial freeform critique without proposals'
agent: 'analyst'
tools: ['search/codebase', 'search/usages']
---

# Pressure-Test Workflow

> You are pressure-testing a design or ADR before any code is written. Find what's wrong **without** proposing alternatives. See [.github/agents/analyst.agent.md](../agents/analyst.agent.md) for the full role definition.

## Step 1: Locate the artifact
1. Identify the design doc (`docs/design/<feature>.md`) or ADR (`docs/adr/NNNN-<slug>.md`) to be critiqued. If the user didn't specify, ask which one.
2. Read it in full. Do not skim — the things you'd skim past are often the things worth challenging.

## Step 2: Gather context
1. Read [.agent/context/project.md](../../.agent/context/project.md) for system background.
2. Read [.agent/context/decisions.md](../../.agent/context/decisions.md) — was this decided before? Is the new design consistent with prior ADRs?
3. Read [.agent/memory/gotchas.md](../../.agent/memory/gotchas.md) — does this design reproduce a known trap?
4. Search the codebase to verify the design's claims about existing systems. Designs that misstate the current code are the easiest critiques and the most valuable.

## Step 3: Pressure-test against all 8 dimensions
Address every dimension in the report. None are optional. If one is genuinely N/A, say so explicitly with a one-sentence justification — silence is not a pass.

1. **Unstated assumptions** — what does this design assume that isn't said?
2. **Failure modes** — how does this break under load, attack, partition, race, malformed input?
3. **Alternatives dismissed too quickly** — what was rejected without serious comparison?
4. **Blast radius** — when this fails, how much breaks?
5. **Reversibility** — is this a one-way door? What's the rollback cost?
6. **Hidden dependencies** — what does this implicitly depend on?
7. **Cost of being wrong** — who pays, and how much?
8. **Test plan adequacy** — does the test plan validate the design, or just the code?

For each finding, cite a specific line or section. Vague claims fail the gate.

## Step 4: Take a stance
- 🟢 **No critical concerns** — design can proceed
- 🟡 **Concerns to address** — Architect must respond before proceeding
- 🔴 **Stop and rethink** — fundamental issues; design needs revision before scaffolding

## Step 5: Test and Validate (mandatory — do not skip)

> A critique that didn't challenge anything is worse than no critique. Validate before delivering.

1. **Dimension coverage** — all 8 dimensions visibly addressed; any N/A justified.
2. **Concreteness** — every finding cites a specific line, section, or file. No vague claims.
3. **Stance** — verdict is explicit at the top of the report.
4. **Boundary** — zero alternative designs, zero rewrites, zero "I'd do it like X."
5. **Positive** — at least one thing the design got right is identified (real, not flattery).
6. **Questions** — every 🔴 and 🟡 finding is paired with an explicit question Architect must answer.
7. **Capture** — critique attached to the design doc or ADR (as a comment, sub-document, or linked file) so Reviewer can verify it was addressed downstream.

## Step 6: Hand off
Return to Architect with the critique. **Do not proceed to Scaffolder.** Only Architect can decide the design is ready to ship — Analyst is a gate, not a step.
