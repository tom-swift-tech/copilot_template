---
name: 'Terraform Conventions'
description: 'Infrastructure-as-code standards for Azure/Terraform'
applyTo: '**/*.tf'
---

# Terraform Conventions

## Module Structure
- `main.tf`, `variables.tf`, `outputs.tf`, `versions.tf`, `providers.tf`
- Variables must have descriptions and type constraints — no untyped `any`
- Outputs must have descriptions for everything downstream consumers need

## Azure Naming
- Pattern: `{org}-{env}-{region}-{service}-{type}` (e.g., `contoso-prod-eus-api-rg`)
- Tag everything: `environment`, `owner`, `project`, `cost-center` at minimum

## State & Safety
- State in Azure Storage Account with state locking
- Use `terraform fmt` and `terraform validate` in CI
- Pin provider versions explicitly
- Use `precondition` / `postcondition` blocks for validation
- Sensitive values marked with `sensitive = true`

## Patterns
- Use `for_each` over `count` for named resources
- Use managed identities over service principals where possible
- Prefer Azure PaaS over IaaS — don't run what Azure can manage
- **Never inline secrets** — use Key Vault references

Refer to [.agent/context/infrastructure.md](../../.agent/context/infrastructure.md) for full enterprise IaC conventions.
