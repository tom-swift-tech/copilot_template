# Cross-Cutting Infrastructure Standards

Standards that apply across Azure, Terraform, Ansible, ServiceNow, and PowerShell.

## Git Workflow

- **Branch strategy**: `main` → `feature/*` → PR → `main`. Always use `main` as default branch.
- **Commit messages**: Conventional Commits (`feat:`, `fix:`, `chore:`, `docs:`, `infra:`).
- **PR requirements**: At least one approval, passing CI, no merge conflicts.
- **Protected branches**: `main` requires PR; no direct push.
- `.gitignore`: Always exclude `.tfstate`, `.tfstate.backup`, `*.tfvars` (secrets), `.vault_pass`, `__pycache__/`, `node_modules/`, `.env`.

## Secret Management Hierarchy

1. **Azure Key Vault** — production secrets, connection strings, certificates.
2. **Ansible Vault** — infrastructure deployment secrets (encrypted at rest in Git).
3. **GitHub Secrets** — CI/CD pipeline variables.
4. **Environment variables** — local development only, never committed.
5. **Never**: Hardcoded in source, `.tfvars`, comments, commit messages, or PR descriptions.

## Error Handling Philosophy

- **Fail fast, fail loud**: Surface errors immediately; never swallow exceptions silently.
- **Structured errors**: Return error objects with context (what failed, where, what to do).
- **Retry with backoff**: For transient failures (network, API rate limits), implement exponential backoff.
- **Idempotent recovery**: Every operation should be safe to retry without side effects.

## Documentation Requirements

| Artifact             | Required Documentation                              |
|----------------------|------------------------------------------------------|
| Terraform module     | `README.md` with usage, inputs table, outputs table  |
| Ansible role         | `README.md` with variables, tags, example playbook   |
| PowerShell module    | Comment-based help on every exported function         |
| API endpoint         | OpenAPI/Swagger spec                                  |
| Architecture change  | ADR (Architecture Decision Record) in `docs/adr/`    |
| Runbook              | Step-by-step in `docs/runbooks/` with rollback steps  |

## Monitoring and Observability

- **Logs**: Structured JSON logging; never `print()` / `Write-Host` for production telemetry.
- **Metrics**: Expose health endpoints (`/healthz`, `/readyz`) on all services.
- **Alerts**: Every production service must have alerts for: availability, error rate, latency p95.
- **Dashboards**: Minimum one dashboard per service showing golden signals (traffic, errors, latency, saturation).
