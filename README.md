# Copilot Project Template — Enterprise Edition

> **Multi-role development framework for GitHub Copilot in VS Code.**
> Adapted from the Claude Code project template with enterprise/infrastructure
> conventions baked in for Azure, Terraform, Ansible, ServiceNow, and PowerShell.

---

## Quick Start

1. **Copy `.github/` and `.vscode/` directories** into your project root
2. **Edit the `Project Context` section** in `.github/copilot-instructions.md`
3. **Uncomment language-specific overrides** at the bottom if needed
4. **Open the project in VS Code** — Copilot auto-loads the instructions
5. **Start chatting** with mode prefixes: `[Architect]`, `[Scaffold]`, `[Build]`, `[Review]`

---

## What's Inside

```
.github/
├── copilot-instructions.md    # Master instructions (auto-loaded by Copilot)
└── COPILOT_CHEATSHEET.md      # Chat patterns, workflow reference, home/work tips
.vscode/
└── tasks.json                 # Pre-review checks, doc generators, TF validate, ansible-lint
docs/
├── design/                    # Feature design documents (Architect output)
└── adr/                       # Architecture Decision Records
standards/                     # (Optional) Extended standards docs
```

---

## The Model

Four roles, advisory phase flow, one hard gate:

```
  Architect → Scaffolder → Builder → Reviewer
  (design)    (structure)   (code)    (gate) ← HARD CHECKPOINT
```

- **Small changes**: Builder → Reviewer
- **Medium features**: Architect (light) → Builder → Reviewer
- **Large systems**: Full flow

Activate via chat prefix: `[Architect] Design a...`, `[Review] Check this...`

---

## Enterprise Additions (vs. Generic Template)

This version includes conventions for:
- **Azure** — resource naming, tagging, managed identities, PaaS-first
- **Terraform** — module structure, state management, validation in CI
- **Ansible** — role skeletons, idempotency requirements, ansible-lint
- **ServiceNow** — scoped apps, update sets, GlideRecord over hardcoded sys_ids
- **PowerShell** — CmdletBinding, verb-noun naming, object output
- **Security** — Key Vault for secrets, no inline credentials, RBAC awareness

---

## Cross-Environment Workflow

Design at home (Claude Code) → implement at work (Copilot):

1. **Home**: Use Claude Code Architect mode to create design docs
2. **Commit**: Push design docs to repo
3. **Work**: Open in VS Code, use `#file:docs/design/<feature>.md` to give Copilot context
4. **Work**: Build mode — implement against the design
5. **Work**: Review mode — quality check before merge

---

**Template by**: Swift Innovative Technology LLC
**License**: MIT
