# ServiceNow Conventions

## Scope Rules

- **All custom tables, scripts, and workflows must be scoped** to an application scope (never Global).
- Scope naming: `x_<vendor>_<app>` (e.g., `x_sit_valor`).
- System properties: prefix with scope name.
- Never modify OOB (out-of-box) records directly — extend or override via scoped app.

## Integration Patterns

| Integration Type      | When to Use                                  |
|-----------------------|----------------------------------------------|
| REST API (Scripted)   | Real-time CRUD from external systems         |
| Import Sets           | Bulk data loads, scheduled syncs             |
| Flow Designer         | No-code/low-code automation within ServiceNow|
| IntegrationHub Spokes | Pre-built connectors (Slack, Azure, etc.)    |
| MID Server            | On-prem to cloud bridging, discovery         |

## Scripting Standards

- **No** direct SQL or `GlideRecord` in client scripts — use GlideAjax for server calls.
- Business Rules: `before` for validation, `after` for side effects, `async` for heavy operations.
- Script Includes: encapsulate reusable logic, mark `client_callable` only if needed.
- Always check ACLs — never assume admin context.
- `gs.info()` for logging; never `gs.print()` in production.

## Change Management

All infrastructure changes must have:

1. A Change Request (CHG) in ServiceNow.
2. CAB approval for `prd` (standard changes pre-approved for `dev`/`stg`).
3. Rollback plan documented in the CHG work notes.
4. Implementation steps referencing the Terraform/Ansible run ID.
