---
name: 'Ansible Conventions'
description: 'Configuration management standards'
applyTo: '**/*.{yml,yaml}'
---

# Ansible Conventions

(Applied when editing YAML files in roles/ or playbooks/ directories)

- Roles over raw tasks — always
- `defaults/main.yml` for tunables, `vars/main.yml` for constants
- Handlers for service restarts — never restart inline
- Use `ansible-lint` in CI
- Idempotency is non-negotiable — every play must be safe to re-run
- Use proper modules — avoid `command`/`shell` where a module exists
- `#Requires` header for any PowerShell scripts called from Ansible

Refer to [.agent/context/infrastructure/ansible.md](../../.agent/context/infrastructure/ansible.md) for full enterprise Ansible conventions.
