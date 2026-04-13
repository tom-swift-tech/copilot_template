# Deployment Standards

> Starter example. Replace or expand for your project.

## Scope

These standards apply to deploying application services and infrastructure changes to shared environments (staging, production). Local development and ephemeral preview environments are exempt.

## Principles

1. **Deployment is a routine event, not a ceremony.** If a deploy is scary, the deploy process is broken — not the courage of the engineer.
2. **Roll back in seconds, not hours.** A deployment without a fast rollback is a one-way door. Don't open one-way doors.
3. **Every change is reversible until it isn't.** Database migrations and customer communications are the irreversibility boundary; everything else should be undoable.
4. **Deploy small, often.** Big-batch deploys hide root causes when they break. Small frequent deploys make the diff that broke it obvious.

## Environments

| Environment | Purpose                                | Data          | Auth gate         |
|-------------|----------------------------------------|---------------|-------------------|
| `dev`       | Engineer workstations and CI           | Synthetic     | None              |
| `staging`   | Pre-production validation              | Sanitized prod copy | Internal SSO |
| `prod`      | Real users                             | Real          | Production auth   |

Staging is a serious environment, not a playground. If something doesn't work in staging it doesn't ship to prod — no exceptions, no "I'll fix it after the deploy."

## Release Strategies

Pick the strategy with the smallest blast radius that meets the requirement:

- **Rolling deploy** — default. Replace instances one at a time. Cheap, slow rollback (have to roll forward through the rolling window).
- **Blue/green** — keep the previous version warm. Instant rollback. Costs 2x infra during the swap.
- **Canary** — route 1-5% of traffic to the new version, observe RED metrics, expand if healthy. Best for high-risk changes.
- **Feature flag** — deploy code dark, flip the flag separately from the deploy. Decouples release from deploy. Use for any change that touches user-visible behavior.

## Pre-Deploy Gate

Every deploy passes through these checks. **None are optional.**

- [ ] All tests green in CI (no skips, no `--no-verify`, no manually re-run failures)
- [ ] Linter and type checker clean
- [ ] Migration plan reviewed (if applicable) — see `DATABASE-DESIGN.md`
- [ ] Rollback path documented in the PR description
- [ ] Feature flag wired up if user-visible
- [ ] Observability in place: new code paths have logs/metrics; new alerts have runbooks
- [ ] Secrets in Key Vault / Parameter Store / Secrets Manager — never in env files committed to the repo
- [ ] No uncommitted local changes

## During Deploy

- **One deploy at a time per service.** Concurrent deploys race and corrupt state.
- The engineer driving the deploy watches it. "Fire and forget" is how outages happen.
- If anything looks wrong — error rate ticking up, latency drifting, alert flapping — **pause and investigate, don't push through**.

## Post-Deploy Validation (mandatory — see Test and Validate below)

- Smoke test against the deployed environment
- Watch RED metrics for at least 10 minutes
- Confirm the change is actually present (version endpoint, build hash, schema version)
- Tag the deploy in the change-tracking system

## Rollback

- Rolling back is a normal operation, not an admission of failure. The team that rolls back without ceremony ships faster overall.
- **Roll back first, diagnose second.** Get back to a known-good state, then investigate from a calm baseline.
- If rolling back is slower than fixing forward (e.g., schema migration already ran), document why in a postmortem and fix the deploy process so the next one isn't trapped.

## Test and Validate (mandatory)

> A deploy is not done when the pipeline turns green. It's done when the change has been observed working in the target environment.

1. **Smoke tests against the deployed environment** — not just against `localhost`. Hit real endpoints with real auth.
2. **RED metrics steady for 10 minutes minimum.** Error rate, latency p95, request rate all within baseline. Don't walk away early.
3. **Version verification.** Hit `/version` (or your equivalent) and confirm the deployed build matches what you intended to ship.
4. **Schema verification (if applicable).** Run `terraform plan` post-apply: must show zero drift. Confirm the migration applied to the expected number of rows.
5. **Rollback rehearsal in non-prod.** For any high-risk deploy, prove the rollback path works in staging *before* deploying to prod.
6. **Observability sanity check.** Open the service dashboard. Confirm new metrics are emitting and new logs are flowing. A deployed change with broken telemetry is half-blind.
7. **Capture the validation evidence** in the deploy ticket / changelog: smoke-test output, dashboard screenshot, version response. Future-you debugging an incident at 2 AM will thank present-you.
