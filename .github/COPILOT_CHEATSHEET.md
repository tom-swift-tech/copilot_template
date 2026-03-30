# Copilot Chat Quick Reference

> How to get the most out of GitHub Copilot Chat in this project.
> The `.github/copilot-instructions.md` file is automatically loaded,
> so Copilot already knows the project conventions and quality standards.

---

## Mode Prefixes

Start your chat message with a mode to set the context:

### Architect Mode
```
[Architect] Design an authentication system that supports both JWT
and API key auth. Output a design doc.
```
```
[Architect] We need a Terraform module for provisioning AKS clusters
with standard FNF tagging. Sketch the interface.
```

### Scaffold Mode
```
[Scaffold] Based on docs/design/auth.md, set up the directory structure
and placeholder types. No implementation.
```
```
[Scaffold] Create an Ansible role skeleton for configure-monitoring
with defaults for Prometheus and Grafana endpoints.
```

### Build Mode (default — prefix optional)
```
Implement the token validation middleware per docs/design/auth.md.
Include error handling and tests.
```
```
Write the Terraform module for the Azure App Service per
docs/design/app-service-module.md.
```

### Review Mode
```
[Review] Review the changes in src/auth/ against our quality standards.
Check for edge cases and error handling gaps.
```
```
[Review] Check this Terraform module for hardcoded values, missing
tags, and destroy-safety.
```

---

## Useful Chat Patterns

### Reference project files for context
```
@workspace #file:docs/design/auth.md Implement the token refresh
endpoint described in this design doc.
```

### Ask for review of selected code
Select code, then:
```
[Review] Check this against our conventions. Flag any anti-patterns.
```

### Generate tests for existing code
Select a function, then:
```
Write tests for this function. Cover happy path, empty input,
error cases, and boundary conditions. Use Arrange-Act-Assert.
```

### Get design feedback
```
[Architect] I'm considering two approaches for the caching layer:
1. In-memory LRU with TTL
2. Azure Redis Cache
What are the trade-offs given our infrastructure constraints?
```

### Bug fix workflow
```
I'm seeing [error description] when [scenario]. Help me:
1. Write a failing test that reproduces this
2. Identify the root cause
3. Implement the minimal fix
```

### Refactor safely
Select code, then:
```
Refactor this to [goal]. Preserve existing behavior exactly.
Show me the changes step by step so I can verify tests pass between each.
```

### Infrastructure patterns
```
[Architect] Design a Terraform module structure for deploying
an Azure Function App with Key Vault integration and managed identity.
```
```
[Build] Write an Ansible playbook to configure nginx reverse proxy
with SSL termination. Must be idempotent.
```
```
[Review] Check this ServiceNow business rule for hardcoded sys_ids,
performance issues, and scope safety.
```

---

## Key `#file` References

Point Copilot to these files when you need it to follow specific standards:

| When you need... | Reference |
|---|---|
| Code conventions | `#file:.github/copilot-instructions.md` (auto-loaded) |
| Feature design | `#file:docs/design/<feature>.md` |
| Quality checklist | Chat: "Check against our Definition of Done" |
| ADR context | `#file:docs/adr/<number>-<slug>.md` |

---

## VS Code Tasks

Run these from the Command Palette (`Ctrl+Shift+P` → `Tasks: Run Task`):

| Task | Purpose |
|---|---|
| **Pre-Review Checks** | Build + lint + test before review |
| **Architect: New Design Doc** | Create a design doc from template |
| **Architect: New ADR** | Create an ADR from template |
| **Terraform: Validate** | Format check + validate + plan |
| **Ansible: Lint** | Run ansible-lint on playbooks |

---

## Limitations vs. Claude Code

Be aware of these differences when switching between home and work:

| Feature | Claude Code (home) | Copilot (work) |
|---------|-------------------|----------------|
| Instructions | 4 separate role prompts | 1 combined file |
| Mode switching | Session declaration | Chat prefix |
| Hooks | Shell scripts via stop hooks | VS Code tasks |
| Context | Full workspace awareness | `#file:` references |
| Persistence | Session-aware | Stateless per chat |
| Tool use | MCP, bash, file ops | Chat + inline only |
| VALOR integration | Native via hooks | N/A |

**Workflow tip**: Design at home with Claude Code (full Architect mode),
then implement at work with Copilot (Build mode) referencing the design
docs you committed.
