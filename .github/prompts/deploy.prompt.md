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

## Post-Deploy
1. Run health checks
2. Monitor logs for 10 minutes
3. Update [.agent/memory/lessons.md](../../.agent/memory/lessons.md) with deployment learnings
4. Update [.agent/memory/gotchas.md](../../.agent/memory/gotchas.md) if issues found
