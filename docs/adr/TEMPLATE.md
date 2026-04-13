# ADR NNNN: {short decision title}

- **Status:** Proposed | Accepted | Rejected | Superseded by ADR-XXXX
- **Date:** YYYY-MM-DD
- **Deciders:** {names or roles}

## Context

{What is the issue that we're seeing that is motivating this decision? Include constraints, forces, and any relevant background. Be specific — "we need better auth" is weaker than "Session tokens are stored in localStorage which fails the new SOC 2 controls for data-at-rest."}

## Decision

{The change we're proposing or have agreed to. State it in one paragraph; details go below.}

## Consequences

**Positive:**
- {What becomes easier or better?}

**Negative:**
- {What becomes harder or introduces risk?}

## Alternatives Considered

1. **{Option A}** — {why rejected}
2. **{Option B}** — {why rejected}

## Validation

How we will know this decision was right — or that it needs to be revisited:

- **Signal we will measure:** {what observable thing tells us this is working}
- **Where we will see it:** {dashboard, log, metric, ticket queue, code review pattern}
- **Threshold for re-evaluation:** {what change in the signal triggers a new ADR}
- **Test coverage required by this decision:** {what new tests this decision implies}
