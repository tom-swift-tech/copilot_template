# Copilot Project Instructions

> This file is automatically loaded by GitHub Copilot for all chat and
> inline completion contexts in this workspace. It defines how Copilot
> should assist across all development phases.

---

## Project Context

<!-- CUSTOMIZE: Replace this section per project -->

- **Project**: [Project Name]
- **Language/Stack**: [e.g., TypeScript / Node.js / Hono]
- **Environment**: Enterprise — FNF internal infrastructure
- **Cloud**: Azure (primary), on-prem where required
- **IaC**: Terraform (Spacelift orchestration where available)
- **Config Management**: Ansible
- **ITSM**: ServiceNow
- **Default Branch**: `main`

---

## Operating Modes

This project uses four mental modes for development. When chatting with
Copilot, prefix your request with the mode to get appropriate responses.

### MODE: Architect
Use when designing systems, choosing patterns, or making structural decisions.
- Produce design docs, not implementation code
- Stubs and type definitions are acceptable, business logic is not
- Always explain the "why" behind design choices
- Output goes to `docs/design/<feature>.md` or `docs/adr/NNNN-<slug>.md`
- Recommend dependencies with justification — do not install them
- When in doubt, prefer simplicity over flexibility
- For infrastructure: include Terraform resource sketches, not full configs
- For integrations: define the ServiceNow/Azure interface contract first

### MODE: Scaffold
Use when setting up project structure, config, and boilerplate.
- Follow design docs exactly — flag deviations, don't silently change
- Create placeholder types/interfaces with `throw new Error("not implemented")`
- Every dependency addition needs a one-line justification
- Verify the project builds and lints clean before considering done
- Produce `.env.example` for any new environment variables
- For Terraform: init module structure with `variables.tf`, `outputs.tf`, `main.tf`
- For Ansible: create role skeleton with `defaults/`, `tasks/`, `handlers/`

### MODE: Build
Use for day-to-day implementation, feature work, and bug fixes. **This is the default mode.**
- Write tests alongside implementation, not after
- Follow the conventions defined below in this document
- Keep commits atomic: one logical change per commit
- Do NOT make architectural changes — flag them for Architect mode
- Do NOT suppress or ignore errors — handle them explicitly
- Every bug fix must include a regression test

### MODE: Review
Use when reviewing code before merge to `main`.
- Check against the quality standards defined below
- Provide specific, actionable feedback with file/line references
- Classify findings: 🔴 Blocker, 🟡 Suggestion, 🟢 Nit
- Do NOT fix the code — describe what needs to change and why
- Always include something positive in the review
- For IaC: verify no secrets in state, confirm destroy-safety
- For ServiceNow: verify scope, sys_ids not hardcoded, update sets clean

---

## Code Conventions

### Naming
| Element | Convention | Example |
|---------|-----------|---------|
| Files/modules | Match ecosystem (`snake_case` or `kebab-case`) | `auth_handler.ts` |
| Functions | `camelCase` (JS/TS) or `snake_case` (Python/Rust/HCL) | `validateToken` |
| Types/Classes | `PascalCase` | `UserProfile` |
| Constants | `SCREAMING_SNAKE_CASE` | `MAX_RETRY_COUNT` |
| Booleans | Prefix: `is`, `has`, `should`, `can` | `isValid` |
| Terraform resources | `snake_case`, descriptive | `azurerm_resource_group.app_rg` |
| Ansible roles | `kebab-case` | `configure-monitoring` |

### Error Handling
- Return errors over throwing when the language supports it
- Every error must include context: what failed, what input, what to do
- Log at the boundary — the function that handles the error logs it
- No empty catch blocks. No swallowed errors. No `unwrap()` without justification.
- For Terraform: use `precondition` / `postcondition` blocks for validation
- For PowerShell: use `try/catch` with `-ErrorAction Stop`, never `-ErrorAction SilentlyContinue` without justification

### Testing
- Test behavior, not implementation — tests should survive refactors
- Test names describe the scenario: `test_returns_404_when_user_not_found`
- Arrange-Act-Assert structure
- No test interdependence — each test sets up its own state
- For Terraform: validate with `terraform plan` as minimum, `terraform test` where supported
- For Ansible: use `--check` mode and verify with assert tasks

### Git
- Branch naming: `<type>/<short-slug>` (e.g., `feat/auth-refresh`, `fix/null-parse`)
- Commit format: `<type>(<scope>): <description>`
- Types: `feat`, `fix`, `refactor`, `test`, `docs`, `chore`, `perf`, `infra`
- Default branch: `main` (always `git branch -M main` after `git init`)
- Each commit should build and pass tests independently
- **Never commit secrets, tokens, or connection strings** — use Key Vault refs or env vars

### Dependencies
- Minimize — every dep is maintenance burden and supply chain risk
- Pin versions, use lockfiles
- Justify additions in commit messages
- Audit for vulnerabilities in CI
- For enterprise: prefer internally-approved packages where available

### Configuration
- Environment variables for deployment-specific values (secrets, URLs, feature flags)
- Config files for structural settings (logging format, retry policies, port numbers)
- Never commit secrets — use `.env.example` with placeholder values
- Validate config at startup — fail fast if required config is missing
- **Azure Key Vault** for production secrets; never inline in Terraform or Ansible

---

## Quality Standards

### Definition of Done
Before any merge to `main`:
- Implementation matches design doc (if one exists)
- All new/changed public APIs have doc comments
- No compiler warnings, linter errors, or formatter violations
- No `TODO`/`FIXME` without a linked issue
- New code has corresponding tests (unit + edge cases)
- Bug fixes include a regression test
- All tests pass (including pre-existing)
- README/CHANGELOG updated if user-facing behavior changed
- For IaC: `terraform plan` shows expected changes only, no drift
- For Ansible: playbook runs idempotent (second run = 0 changes)

### Quality Tiers
- **Tier 1 (Critical)**: Full coverage, design doc required. Auth, data, security, public APIs, production IaC.
- **Tier 2 (Standard)**: Good coverage, happy path + key edges. Most feature code, internal APIs.
- **Tier 3 (Tooling)**: Basic coverage, non-obvious behavior. Scripts, helpers, migrations, dev-only IaC.
- **Tier 4 (Prototype)**: Minimal. Clearly marked experimental. Never merges to `main` as-is.

### Anti-Patterns to Flag
- God objects/functions — doing too many things
- Stringly-typed data — strings where enums/types belong
- Copy-paste duplication — should be abstracted
- Magic numbers — unnamed constants
- Silent failures — errors swallowed without logging
- Test-free changes — code changes without test changes
- Hardcoded sys_ids or GUIDs — use lookups or variables
- Monolithic Terraform roots — break into composable modules
- Ansible `command`/`shell` where a proper module exists

---

## Enterprise / Infrastructure Conventions

### Azure
- Resource naming: `{org}-{env}-{region}-{service}-{type}` (e.g., `fnf-prod-eus-api-rg`)
- Tag everything: `environment`, `owner`, `project`, `cost-center` at minimum
- Use managed identities over service principals where possible
- Prefer Azure PaaS over IaaS — don't run what Azure can manage

### Terraform
- Module structure: `main.tf`, `variables.tf`, `outputs.tf`, `versions.tf`, `providers.tf`
- State in Azure Storage Account with state locking
- Use `terraform fmt` and `terraform validate` in CI
- Variables have descriptions and type constraints — no untyped `any`
- Outputs for everything downstream consumers need

### Ansible
- Roles over raw tasks — always
- `defaults/main.yml` for tunables, `vars/main.yml` for constants
- Handlers for service restarts — never restart inline
- Use `ansible-lint` in CI
- Idempotency is non-negotiable — every play must be safe to re-run

### ServiceNow
- Never hardcode sys_ids — use GlideRecord lookups or sys_properties
- Update sets for all changes — no direct production edits
- Script includes over inline scripts for reusable logic
- Business rules: prefer async where possible, keep before-rules fast
- Scoped apps over global scope for new development

### PowerShell
- Use `#Requires -Version 7` minimum for cross-platform compatibility
- Verb-Noun naming for functions: `Get-ServiceStatus`, `Set-ConfigValue`
- Use `[CmdletBinding()]` and proper parameter validation
- Output objects, not strings — let the consumer format
- Use `$ErrorActionPreference = 'Stop'` at script level

---

## Response Preferences

When assisting in this workspace, Copilot should:

- **Prefer explicit over clever** — readable code wins over terse code
- **Include error handling** in generated code — never produce happy-path-only examples
- **Add brief inline comments** for non-obvious logic, but don't over-comment
- **Follow existing patterns** in the codebase over general best practices when they conflict
- **Ask clarifying questions** when requirements are ambiguous rather than guessing
- **Show trade-offs** when multiple approaches exist — don't silently pick one
- **Enterprise-aware**: consider RBAC, audit logging, and compliance implications
- **Infrastructure-aware**: when touching IaC, consider blast radius and rollback strategy
