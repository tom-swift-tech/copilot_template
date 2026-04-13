# Azure Conventions

## Naming Convention

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

## Tagging Policy

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

## Architecture Preferences

1. **PaaS-first**: Prefer managed services over IaaS (App Service over VM, Azure SQL over SQL on VM, AKS over self-managed K8s).
2. **Hub-spoke networking**: All workload VNets peer to a central hub containing shared services (firewall, DNS, bastion).
3. **Private endpoints**: All data services accessed via Private Link; no public endpoints in `prd`.
4. **Managed Identity over service principals**: Use system-assigned or user-assigned managed identity for service-to-service auth. Never store credentials in code.
5. **Key Vault for secrets**: All secrets, certs, and connection strings in Key Vault. Reference via Key Vault references in App Service/Function App configuration.
6. **Immutable deployments**: Prefer slot-based or blue/green deployments; never patch production in place.
