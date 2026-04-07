# Infrastructure Conventions

> Enterprise IaC standards for Azure, Terraform, Ansible, ServiceNow, and PowerShell.
> This file is the single source of truth for infrastructure-as-code patterns.
> Loaded by all agents; enforced by Reviewer.

---

## Azure

### Naming Convention

All Azure resources follow: `{prefix}-{env}-{region}-{service}-{instance}`

| Token      | Values                                      |
|------------|---------------------------------------------|
| `prefix`   | Organization or project abbreviation        |
| `env`      | `dev`, `stg`, `prd`                         |
| `region`   | `eus`, `eus2`, `cus`, `wus`, `wus2`, etc.  |
| `service`  | Resource-type abbreviation (see table below)|
| `instance` | Zero-padded ordinal: `01`, `02`, ...        |

**Resource abbreviations:**

| Resource                | Abbreviation |
|-------------------------|-------------|
| Resource Group          | `rg`        |
| Virtual Network         | `vnet`      |
| Subnet                  | `snet`      |
| Network Security Group  | `nsg`       |
| Public IP               | `pip`       |
| Load Balancer           | `lb`        |
| Application Gateway     | `agw`       |
| Virtual Machine         | `vm`        |
| Storage Account         | `st` (no hyphens, 3–24 chars, lowercase) |
| Key Vault               | `kv` (no hyphens, 3–24 chars)            |
| App Service Plan        | `asp`       |
| App Service / Web App   | `app`       |
| Function App            | `func`      |
| SQL Server              | `sql`       |
| SQL Database            | `sqldb`     |
| Cosmos DB               | `cosmos`    |
| AKS Cluster             | `aks`       |
| Container Registry      | `cr` (no hyphens, 5–50 chars, alphanumeric) |
| Log Analytics Workspace | `log`       |
| Application Insights    | `appi`      |
| Service Bus             | `sb`        |
| Event Hub               | `evh`       |

**Examples:**
- `contoso-prd-eus2-aks-01` — Production AKS cluster in East US 2
- `contosoprdeus2st01` — Production storage account (no hyphens)
- `contoso-dev-cus-vm-01` — Dev VM in Central US

### Tagging Policy

Every resource **must** have these tags:

| Tag            | Description                        | Example            |
|----------------|------------------------------------|--------------------|
| `Environment`  | Deployment tier                    | `Production`       |
| `Owner`        | Responsible team or individual     | `Platform-Eng`     |
| `CostCenter`   | Chargeback code                    | `CC-4420`          |
| `Application`  | App or service name                | `VALOR`            |
| `ManagedBy`    | IaC tool that owns the resource    | `Terraform`        |
| `CreatedDate`  | ISO 8601 creation date             | `2026-01-15`       |

Optional but recommended: `DataClassification`, `SLA`, `Compliance`.

### Architecture Preferences

1. **PaaS-first**: Prefer managed services over IaaS (App Service over VM, Azure SQL over SQL on VM, AKS over self-managed K8s).
2. **Hub-spoke networking**: All workload VNets peer to a central hub containing shared services (firewall, DNS, bastion).
3. **Private endpoints**: All data services accessed via Private Link; no public endpoints in `prd`.
4. **Managed Identity over service principals**: Use system-assigned or user-assigned managed identity for service-to-service auth. Never store credentials in code.
5. **Key Vault for secrets**: All secrets, certs, and connection strings in Key Vault. Reference via Key Vault references in App Service/Function App configuration.
6. **Immutable deployments**: Prefer slot-based or blue/green deployments; never patch production in place.

---

## Terraform

### File Structure

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

### State Management

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

### Proxmox / Homelab State

For homelab Proxmox IaC (bpg/proxmox provider):

- Backend: Spacelift-managed or local (never remote Azure for homelab).
- Provider version: Pin to `~> 0.95` (bpg/proxmox).
- Always use `main` as the default branch: include `git branch -M main` after `git init`.
- Spacelift integration: GitHub org `Spacelift-Swift`, worker pool `homelab-worker`.
- Cloud-init `user_account.username` must match Ansible `ansible_user` in inventory.

### Module Conventions

- Modules are reusable, parameterized, and version-pinned.
- Source from private registry or Git tags: `source = "git::https://...?ref=v1.2.0"`
- Every module has a `README.md` with usage example.
- No hardcoded values in modules — everything via variables.
- Outputs: expose `id`, `name`, and any connection info downstream modules need.

### Coding Standards

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

### Security

- Never put secrets in `.tfvars` — use Key Vault data sources or environment variables.
- Enable `checkov` or `tfsec` in CI for policy scanning.
- Sensitive outputs: `sensitive = true`.
- No `*` in NSG or firewall rules — explicit CIDR blocks only.

---

## Ansible

### Project Structure

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

### Idempotency Rules

Every task **must** be idempotent — running a playbook twice produces no changes on the second run.

| Pattern                    | Do                                               | Don't                                  |
|----------------------------|--------------------------------------------------|----------------------------------------|
| Package install            | `state: present`                                 | `state: latest` (non-deterministic)    |
| File management            | `ansible.builtin.template` / `copy`              | Raw `shell: echo > file`              |
| Service management         | `ansible.builtin.systemd` with `state`           | `shell: systemctl start ...`           |
| User creation              | `ansible.builtin.user` with guards               | `shell: useradd ...`                   |
| Command execution          | `creates:` / `removes:` / `when:` guard          | Unguarded `command` / `shell`          |
| Config changes             | `notify: restart <service>` handler              | Inline `systemctl restart`             |

### Cloud-Init Integration

When provisioning VMs bootstrapped with cloud-init (especially Proxmox):

1. `cloud-init status --wait` returns rc=2 for recoverable warnings — accept `[0, 2]`.
2. SSH user must match the Terraform `user_account.username`.
3. If the SSH user IS the service user, use `getent` check + `usermod -aG` instead of the full `user` module (avoids process conflict).
4. Stop services before modifying users that run them.

See the `ansible-proxmox-cloudinit` skill for detailed patterns and templates.

### Coding Standards

- Use FQCNs: `ansible.builtin.copy`, not `copy`.
- `ansible-lint` before every commit (enforced by `.vscode/tasks.json`).
- `changed_when` and `failed_when` on every `command`/`shell` task.
- No hardcoded IPs or hostnames — use inventory variables.
- Secrets via Ansible Vault or environment variables — never plaintext in playbooks.
- Role variables: defaults in `defaults/main.yml` (overridable), constants in `vars/main.yml`.
- Tags on every task for selective execution: `tags: [install, configure, deploy]`.
- Handlers over inline restarts — always.

### Vault Usage

```bash
# Encrypt a variable file
ansible-vault encrypt inventory/prd/group_vars/secrets.yml

# Use in playbook
ansible-playbook -i inventory/prd/hosts.yml playbooks/site.yml --ask-vault-pass
```

- One vault password per environment (dev vault ≠ prd vault).
- Prefix encrypted variable names: `vault_db_password`, then assign `db_password: "{{ vault_db_password }}"` in `group_vars/all.yml`.

---

## ServiceNow

### Scope Rules

- **All custom tables, scripts, and workflows must be scoped** to an application scope (never Global).
- Scope naming: `x_<vendor>_<app>` (e.g., `x_sit_valor`).
- System properties: prefix with scope name.
- Never modify OOB (out-of-box) records directly — extend or override via scoped app.

### Integration Patterns

| Integration Type      | When to Use                                  |
|-----------------------|----------------------------------------------|
| REST API (Scripted)   | Real-time CRUD from external systems         |
| Import Sets           | Bulk data loads, scheduled syncs             |
| Flow Designer         | No-code/low-code automation within ServiceNow|
| IntegrationHub Spokes | Pre-built connectors (Slack, Azure, etc.)    |
| MID Server            | On-prem to cloud bridging, discovery         |

### Scripting Standards

- **No** direct SQL or `GlideRecord` in client scripts — use GlideAjax for server calls.
- Business Rules: `before` for validation, `after` for side effects, `async` for heavy operations.
- Script Includes: encapsulate reusable logic, mark `client_callable` only if needed.
- Always check ACLs — never assume admin context.
- `gs.info()` for logging; never `gs.print()` in production.

### Change Management

All infrastructure changes must have:

1. A Change Request (CHG) in ServiceNow.
2. CAB approval for `prd` (standard changes pre-approved for `dev`/`stg`).
3. Rollback plan documented in the CHG work notes.
4. Implementation steps referencing the Terraform/Ansible run ID.

---

## PowerShell

### Module Structure

```
ModuleName/
├── ModuleName.psd1          # Module manifest
├── ModuleName.psm1          # Root module (dot-sources functions)
├── Public/
│   └── <Verb>-<Noun>.ps1   # Exported functions
├── Private/
│   └── helpers.ps1          # Internal functions
└── Tests/
    └── ModuleName.Tests.ps1 # Pester tests
```

### Coding Standards

```powershell
function Get-ServerHealth {
    [CmdletBinding()]
    param(
        [Parameter(Mandatory, ValueFromPipeline)]
        [ValidateNotNullOrEmpty()]
        [string[]]$ComputerName,

        [Parameter()]
        [ValidateSet('Basic', 'Full')]
        [string]$ReportType = 'Basic'
    )

    begin {
        # One-time setup
    }

    process {
        foreach ($computer in $ComputerName) {
            # Per-object processing with proper error handling
            try {
                # ...
            }
            catch {
                Write-Error "Failed to query ${computer}: $_"
            }
        }
    }

    end {
        # Cleanup
    }
}
```

**Mandatory patterns:**

- `[CmdletBinding()]` on every function.
- `Verb-Noun` naming from the approved verb list (`Get-Verb`).
- Pipeline support: `[Parameter(ValueFromPipeline)]` where it makes sense.
- `$ErrorActionPreference = 'Stop'` at script scope for scripts (not modules).
- Structured error handling: `try/catch` with `Write-Error`, never `throw` in exported functions (breaks pipeline).
- Output objects, not strings: return `[PSCustomObject]@{...}`, never `Write-Host` for data.
- `#Requires -Version 7.0` or appropriate minimum at top of scripts.

### Security

- Never store credentials in scripts — use `Get-Credential`, `SecretManagement` module, or Key Vault.
- `SecureString` for any password parameters.
- Sign scripts with a code-signing certificate in production environments.
- Execution policy: `RemoteSigned` minimum, `AllSigned` for production.

### Pester Testing

```powershell
Describe 'Get-ServerHealth' {
    Context 'When server is reachable' {
        It 'Returns a health object with expected properties' {
            $result = Get-ServerHealth -ComputerName 'localhost'
            $result | Should -Not -BeNullOrEmpty
            $result.PSObject.Properties.Name | Should -Contain 'Status'
        }
    }

    Context 'When server is unreachable' {
        It 'Writes an error without terminating' {
            { Get-ServerHealth -ComputerName 'fake-server' } |
                Should -Not -Throw
        }
    }
}
```

- Every exported function has a corresponding Pester test.
- Use `InModuleScope` for testing private functions.
- Mock external dependencies (`Mock Invoke-RestMethod ...`).

---

## Cross-Cutting Standards

### Git Workflow

- **Branch strategy**: `main` → `feature/*` → PR → `main`. Always use `main` as default branch.
- **Commit messages**: Conventional Commits (`feat:`, `fix:`, `chore:`, `docs:`, `infra:`).
- **PR requirements**: At least one approval, passing CI, no merge conflicts.
- **Protected branches**: `main` requires PR; no direct push.
- `.gitignore`: Always exclude `.tfstate`, `.tfstate.backup`, `*.tfvars` (secrets), `.vault_pass`, `__pycache__/`, `node_modules/`, `.env`.

### Secret Management Hierarchy

1. **Azure Key Vault** — production secrets, connection strings, certificates.
2. **Ansible Vault** — infrastructure deployment secrets (encrypted at rest in Git).
3. **GitHub Secrets** — CI/CD pipeline variables.
4. **Environment variables** — local development only, never committed.
5. **Never**: Hardcoded in source, `.tfvars`, comments, commit messages, or PR descriptions.

### Error Handling Philosophy

- **Fail fast, fail loud**: Surface errors immediately; never swallow exceptions silently.
- **Structured errors**: Return error objects with context (what failed, where, what to do).
- **Retry with backoff**: For transient failures (network, API rate limits), implement exponential backoff.
- **Idempotent recovery**: Every operation should be safe to retry without side effects.

### Documentation Requirements

| Artifact             | Required Documentation                              |
|----------------------|------------------------------------------------------|
| Terraform module     | `README.md` with usage, inputs table, outputs table  |
| Ansible role         | `README.md` with variables, tags, example playbook   |
| PowerShell module    | Comment-based help on every exported function         |
| API endpoint         | OpenAPI/Swagger spec                                  |
| Architecture change  | ADR (Architecture Decision Record) in `docs/adr/`    |
| Runbook              | Step-by-step in `docs/runbooks/` with rollback steps  |

### Monitoring and Observability

- **Logs**: Structured JSON logging; never `print()` / `Write-Host` for production telemetry.
- **Metrics**: Expose health endpoints (`/healthz`, `/readyz`) on all services.
- **Alerts**: Every production service must have alerts for: availability, error rate, latency p95.
- **Dashboards**: Minimum one dashboard per service showing golden signals (traffic, errors, latency, saturation).
