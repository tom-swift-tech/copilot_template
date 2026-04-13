# Ansible Conventions

## Project Structure

```
ansible/
├── inventory/
│   ├── dev/
│   │   ├── hosts.yml
│   │   └── group_vars/
│   └── prd/
│       ├── hosts.yml
│       └── group_vars/
├── playbooks/
│   ├── site.yml           # Master playbook (imports roles)
│   └── <purpose>.yml      # Single-purpose playbooks
├── roles/
│   └── <role-name>/
│       ├── tasks/main.yml
│       ├── handlers/main.yml
│       ├── templates/
│       ├── files/
│       ├── vars/main.yml
│       └── defaults/main.yml
├── ansible.cfg
└── requirements.yml       # Galaxy role/collection dependencies
```

## Idempotency Rules

Every task **must** be idempotent — running a playbook twice produces no changes on the second run.

| Pattern                    | Do                                               | Don't                                  |
|----------------------------|--------------------------------------------------|----------------------------------------|
| Package install            | `state: present`                                 | `state: latest` (non-deterministic)    |
| File management            | `ansible.builtin.template` / `copy`              | Raw `shell: echo > file`              |
| Service management         | `ansible.builtin.systemd` with `state`           | `shell: systemctl start ...`           |
| User creation              | `ansible.builtin.user` with guards               | `shell: useradd ...`                   |
| Command execution          | `creates:` / `removes:` / `when:` guard          | Unguarded `command` / `shell`          |
| Config changes             | `notify: restart <service>` handler              | Inline `systemctl restart`             |

## Cloud-Init Integration

When provisioning VMs bootstrapped with cloud-init (especially Proxmox):

1. `cloud-init status --wait` returns rc=2 for recoverable warnings — accept `[0, 2]`.
2. SSH user must match the Terraform `user_account.username`.
3. If the SSH user IS the service user, use `getent` check + `usermod -aG` instead of the full `user` module (avoids process conflict).
4. Stop services before modifying users that run them.

See the `ansible-proxmox-cloudinit` skill for detailed patterns and templates.

## Coding Standards

- Use FQCNs: `ansible.builtin.copy`, not `copy`.
- `ansible-lint` before every commit (enforced by `.vscode/tasks.json`).
- `changed_when` and `failed_when` on every `command`/`shell` task.
- No hardcoded IPs or hostnames — use inventory variables.
- Secrets via Ansible Vault or environment variables — never plaintext in playbooks.
- Role variables: defaults in `defaults/main.yml` (overridable), constants in `vars/main.yml`.
- Tags on every task for selective execution: `tags: [install, configure, deploy]`.
- Handlers over inline restarts — always.

## Vault Usage

```bash
# Encrypt a variable file
ansible-vault encrypt inventory/prd/group_vars/secrets.yml

# Use in playbook
ansible-playbook -i inventory/prd/hosts.yml playbooks/site.yml --ask-vault-pass
```

- One vault password per environment (dev vault ≠ prd vault).
- Prefix encrypted variable names: `vault_db_password`, then assign `db_password: "{{ vault_db_password }}"` in `group_vars/all.yml`.
