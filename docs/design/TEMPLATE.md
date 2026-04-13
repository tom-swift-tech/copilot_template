# Design: {feature or change name}

- **Author:** {name}
- **Status:** Draft | Review | Approved | Implemented
- **Last updated:** YYYY-MM-DD
- **Related ADRs:** {link if applicable}

## Problem

{What are we trying to solve, for whom, and why now? One or two paragraphs. Include measurable signals if you have them.}

## Goals

- {Concrete outcome 1}
- {Concrete outcome 2}

## Non-goals

- {Explicitly out of scope — saves future reviewers from re-litigating}

## Proposal

{The design itself. Include diagrams, data flow, API surfaces, schema changes — whatever a reader needs to understand and critique the approach. Keep prose tight; show, don't tell.}

### Data model

{Tables, schemas, or structures.}

### API / interface

{Endpoints, function signatures, CLI commands.}

### Rollout

{How this lands: feature flag, migration, backfill, phased enablement.}

## Test Plan (required)

Builder cannot validate what was never specified. This section is mandatory.

- **Unit tests:** {which behaviors, which boundaries, which error cases — name the modules}
- **Integration tests:** {which seams, which contracts, which external systems are stubbed vs. real}
- **End-to-end / smoke:** {what a human or CI hits to confirm the feature works in a real environment}
- **Manual validation:** {what a reviewer checks by hand before sign-off}
- **Rollback test:** {how we prove the rollback works *before* we need it in prod}
- **Observability check:** {which metrics/logs confirm the feature is healthy post-deploy}

## Alternatives Considered

1. **{Option A}** — {why rejected}
2. **{Option B}** — {why rejected}

## Open Questions

- {Things still unresolved that need input before approval}

## Risks

- {What could go wrong in implementation or operation, and how we'd detect it}
