---
description: 'Pre-deploy checklist and deployment workflow'
agent: 'builder'
tools: ['terminal/runCommand']
---

# Deployment Workflow

## Pre-Deploy Checklist
- [ ] All tests pass
- [ ] Linting clean
- [ ] No uncommitted changes (`git status`)
- [ ] Environment variables documented in [.agent/context/stack.md](../../.agent/context/stack.md)
- [ ] Database migrations ready (if applicable)
- [ ] For IaC: `terraform plan` shows expected changes only, no drift
- [ ] For Ansible: playbook runs idempotent (second run = 0 changes)
- [ ] No secrets in code or state files

## Test and Validate (mandatory — do not skip)
> A deploy is not complete until post-deploy validation passes against the live target.
1. **Smoke tests against the deployed environment** — not just localhost. Hit real endpoints with real auth.
2. Run health checks on every service touched by the deploy.
3. Verify the change is actually present (version endpoint, build hash, schema migration applied).
4. Check observability: error rate, latency, and saturation are within baseline for at least 10 minutes.
5. For IaC: `terraform plan` post-apply shows zero drift.
6. For DB migrations: confirm the new schema and verify rollback path works in a non-prod target.
7. If anything is off — **roll back first, diagnose second**.

## Post-Deploy
1. Update [.agent/memory/lessons.md](../../.agent/memory/lessons.md) with deployment learnings
2. Update [.agent/memory/gotchas.md](../../.agent/memory/gotchas.md) if issues found
