# Terraform Conventions

## File Structure

```
infra/
├── main.tf              # Provider config, backend, data sources
├── variables.tf         # All variable declarations (no defaults for secrets)
├── outputs.tf           # All outputs
├── locals.tf            # Computed values, name construction, tag merging
├── terraform.tfvars     # Non-secret variable values (env-specific)
├── versions.tf          # Required providers with version constraints
├── <resource>.tf        # One file per logical resource group
└── modules/
    └── <module-name>/
        ├── main.tf
        ├── variables.tf
        └── outputs.tf
```

## State Management

- **Remote state**: Always use `azurerm` backend with state locking.
- **State per environment**: Separate state files per env (`dev.tfstate`, `prd.tfstate`).
- **Never** commit `.tfstate` or `.tfstate.backup` to git.
- **Import before create**: If a resource already exists, `terraform import` it; never recreate.

```hcl
terraform {
  backend "azurerm" {
    resource_group_name  = "rg-terraform-state"
    storage_account_name = "stterraformstate01"
    container_name       = "tfstate"
    key                  = "project/env.tfstate"
  }
}
```

## Proxmox / Homelab State

For homelab Proxmox IaC (bpg/proxmox provider):

- Backend: Spacelift-managed or local (never remote Azure for homelab).
- Provider version: Pin to `~> 0.95` (bpg/proxmox).
- Always use `main` as the default branch: include `git branch -M main` after `git init`.
- Spacelift integration: GitHub org `Spacelift-Swift`, worker pool `homelab-worker`.
- Cloud-init `user_account.username` must match Ansible `ansible_user` in inventory.

## Module Conventions

- Modules are reusable, parameterized, and version-pinned.
- Source from private registry or Git tags: `source = "git::https://...?ref=v1.2.0"`
- Every module has a `README.md` with usage example.
- No hardcoded values in modules — everything via variables.
- Outputs: expose `id`, `name`, and any connection info downstream modules need.

## Coding Standards

```hcl
# Good: descriptive resource names, consistent formatting
resource "azurerm_resource_group" "main" {
  name     = local.resource_group_name
  location = var.location
  tags     = local.common_tags
}

# Good: lifecycle rules for stateful resources
resource "azurerm_storage_account" "data" {
  # ...
  lifecycle {
    prevent_destroy = true
  }
}
```

- Use `locals` for name construction — never inline string interpolation in resource blocks.
- Use `for_each` over `count` when resources have meaningful keys.
- Mark stateful resources with `lifecycle { prevent_destroy = true }`.
- Use `moved` blocks for refactoring — never destroy and recreate.
- `terraform fmt` and `terraform validate` before every commit (enforced by `.vscode/tasks.json`).
- Pin provider versions with pessimistic constraint: `~> x.y`.

## Security

- Never put secrets in `.tfvars` — use Key Vault data sources or environment variables.
- Enable `checkov` or `tfsec` in CI for policy scanning.
- Sensitive outputs: `sensitive = true`.
- No `*` in NSG or firewall rules — explicit CIDR blocks only.
