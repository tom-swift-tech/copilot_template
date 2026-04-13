# Infrastructure Conventions — Index

> Enterprise IaC standards for Azure, Terraform, Ansible, ServiceNow, and PowerShell.
> Split into topic files so agents only load the area they're working in.

| Topic        | File                                        | Load when editing                |
|--------------|---------------------------------------------|----------------------------------|
| Azure        | [infrastructure/azure.md](./infrastructure/azure.md)               | Azure resources, ARM, Bicep      |
| Terraform    | [infrastructure/terraform.md](./infrastructure/terraform.md)       | `**/*.tf`, modules               |
| Ansible      | [infrastructure/ansible.md](./infrastructure/ansible.md)           | playbooks, roles, inventory      |
| ServiceNow   | [infrastructure/servicenow.md](./infrastructure/servicenow.md)     | scoped apps, Business Rules      |
| PowerShell   | [infrastructure/powershell.md](./infrastructure/powershell.md)     | `**/*.{ps1,psm1,psd1}`           |
| Cross-cutting| [infrastructure/cross-cutting.md](./infrastructure/cross-cutting.md) | git, secrets, errors, observability |

## How to use this index

- **Agents**: Read only the topic file(s) relevant to the current task. Cross-cutting always applies.
- **Reviewers**: Load the topic file matching the files in the PR. Reviewer enforces these standards.
- **Scaffolders**: Read the relevant topic(s) before generating structure.

## Loading order

1. Topic file(s) for the technologies being touched.
2. `cross-cutting.md` — always.
3. `.agent/memory/gotchas.md` — always.
