# Folder Agent — Enterprise Template

> **The folder structure IS the agent.** No framework, no orchestration code — just well-organized files that any LLM can navigate.

A two-layer template for enterprise IaC projects that works natively with GitHub Copilot and Claude Code simultaneously.

## Quick Start

1. Copy this template into your project root.
2. Read [docs/GETTING-STARTED.md](docs/GETTING-STARTED.md) — the Day 1 runbook.
3. Fill in `.agent/context/project.md` and `.agent/context/stack.md` with your project details.
4. Open in VS Code with GitHub Copilot enabled.
5. Select an agent from the Copilot agent dropdown (**Builder** is the default).
6. Use slash commands: `/debug`, `/feature`, `/review`, `/deploy`, etc.

For Claude Code: `CLAUDE.md` and `AGENTS.md` are auto-loaded. All `.agent/` context is available.

## Architecture

```
┌─────────────────────────────────────────────────────┐
│                    Your Project                      │
├──────────────────────┬──────────────────────────────┤
│  .github/ (Copilot)  │  .agent/ (LLM-Agnostic)     │
│                      │                              │
│  agents/             │  context/                    │
│    architect.agent   │    project.md                │
│    analyst.agent     │    conventions.md            │
│    scaffolder.agent  │    infrastructure.md         │
│    builder.agent     │    stack.md                  │
│    reviewer.agent    │    decisions.md              │
│  instructions/       │                              │
│    rust, ts, python  │  tasks/                      │
│    terraform, ansible│    current.md                │
│    powershell, docs  │    backlog.md                │
│                      │    done/                     │
│  prompts/            │                              │
│    /debug, /feature  │  memory/                     │
│    /refactor, /review│    lessons.md                │
│    /test, /deploy    │    gotchas.md                │
│    /status           │    patterns.md               │
│    /update-memory    │                              │
│                      │                              │
│  skills/             │                              │
│    update-readme/    │                              │
│    sync-agents/      │                              │
├──────────────────────┴──────────────────────────────┤
│  CLAUDE.md          — Claude Code entry point        │
│  AGENTS.md          — Agent definitions (both tools) │
│  docs/              — ADRs, design docs, GETTING-STARTED │
│  standards/         — Extended standards (API, DB,    │
│                       observability, deploy, security)│
│  .vscode/tasks.json — VS Code build/lint tasks       │
└─────────────────────────────────────────────────────┘
```

> **Mandatory rule baked into every workflow:** every prompt and every agent handoff ends with **Test and Validate**. Engineers don't need to remember it — the prompts won't let them skip it.

## Agent Personas

| Agent        | Mode        | Access          | Purpose                                  |
|-------------|-------------|-----------------|------------------------------------------|
| **Architect** | Read-only   | No code edits   | Design docs, ADRs, architecture diagrams |
| **Analyst**   | Read-only   | No code edits   | Pressure-test designs and ADRs (freeform critique, never proposals) |
| **Scaffolder**| Structure   | Config & boilerplate | Directory setup, CI/CD, dependency config|
| **Builder**   | Full (default)| Read/write all | Features, bug fixes, refactoring, tests  |
| **Reviewer**  | Read-only   | No code edits   | Code review, quality gates, security audit|

**Handoff flow**: Architect → Analyst → Scaffolder → Builder → Reviewer → (merge or back to Builder)

Analyst is a *gate*, not a step — findings always return to Architect, who decides whether to revise. Skip Analyst for bug fixes, refactors, and trivial features. See [AGENTS.md](AGENTS.md) for the full handoff protocol.

## Slash Commands

| Command            | Description                                      |
|--------------------|--------------------------------------------------|
| `/debug`           | Systematic debugging with log analysis           |
| `/feature`         | End-to-end feature implementation workflow        |
| `/refactor`        | Refactoring with safety checks and tests         |
| `/review`          | Code review against conventions and anti-patterns|
| `/pressure-test`   | Adversarial critique of a design doc or ADR (Analyst) |
| `/test`            | Test generation for specified code                |
| `/deploy`          | Deployment checklist and execution                |
| `/status`          | Project status from tasks and recent changes      |
| `/update-memory`   | Capture session learnings into memory files        |

## Language Instructions

Scoped instructions load automatically when editing matching file types:

| Language    | File Pattern                   | File                                      |
|-------------|-------------------------------|-------------------------------------------|
| Rust        | `**/*.rs`                     | `.github/instructions/rust.instructions.md`       |
| TypeScript  | `**/*.{ts,tsx}`               | `.github/instructions/typescript.instructions.md` |
| Python      | `**/*.py`                     | `.github/instructions/python.instructions.md`     |
| Terraform   | `**/*.tf`                     | `.github/instructions/terraform.instructions.md`  |
| Ansible     | `**/*.{yml,yaml}`             | `.github/instructions/ansible.instructions.md`    |
| PowerShell  | `**/*.{ps1,psm1,psd1}`       | `.github/instructions/powershell.instructions.md` |
| Docs        | `docs/**/*.md`                | `.github/instructions/docs.instructions.md`       |

## Context Files

| File                               | Purpose                                          |
|------------------------------------|--------------------------------------------------|
| `.agent/context/project.md`        | Project name, architecture, contacts              |
| `.agent/context/conventions.md`    | Naming, error handling, testing, dependencies     |
| `.agent/context/infrastructure.md` | Azure, Terraform, Ansible, ServiceNow, PowerShell |
| `.agent/context/stack.md`          | Runtime, toolchain, and infrastructure components |
| `.agent/context/decisions.md`      | Architecture Decision Records                     |
| `.agent/tasks/current.md`          | Active work items (max 3–5)                       |
| `.agent/tasks/backlog.md`          | Prioritized upcoming work                         |
| `.agent/memory/lessons.md`         | Hard-won knowledge from past work                 |
| `.agent/memory/gotchas.md`         | Specific traps and their fixes                    |
| `.agent/memory/patterns.md`        | Reusable solutions worth repeating                |

## VS Code Tasks

Pre-configured tasks in `.vscode/tasks.json`:

- **Pre-Review Check** — lint + validate before code review
- **Generate Design Doc** — scaffold from Architect output
- **Generate ADR** — new architecture decision record
- **Terraform Validate** — `terraform fmt -check && terraform validate`
- **Ansible Lint** — `ansible-lint` on playbooks

## Standards

Extended standards live in [standards/](standards/). Each is a starter you replace or expand:

| Standard | Topic |
|----------|-------|
| [API-DESIGN.md](standards/API-DESIGN.md) | HTTP API conventions |
| [DATABASE-DESIGN.md](standards/DATABASE-DESIGN.md) | Schema, migrations, query safety |
| [OBSERVABILITY.md](standards/OBSERVABILITY.md) | Logs, metrics, traces, alerting |
| [DEPLOYMENT.md](standards/DEPLOYMENT.md) | Release strategies and rollback |
| [SECURITY.md](standards/SECURITY.md) | Auth, secrets, input validation |

Every standard ends with a mandatory **Test and Validate** section.

## Customizing This Template

1. **Add a language**: Create `.github/instructions/<lang>.instructions.md` with an `applyTo` front matter pattern.
2. **Add a slash command**: Create `.github/prompts/<name>.prompt.md` with front matter. End it with a `Test and Validate` step.
3. **Add an agent**: Create `.github/agents/<name>.agent.md`, register it in `AGENTS.md` and `.github/copilot-instructions.md`, then run the `sync-agents` skill to confirm no drift.
4. **Add context**: Create a new `.md` file in `.agent/context/` and reference it in `CLAUDE.md` and `AGENTS.md`.
5. **Add a standard**: Drop a `<NAME>.md` in `standards/`, add a row to `standards/README.md`, end with a `Test and Validate` section.

## Compatibility

| Tool          | Entry Point      | Auto-Discovery                |
|---------------|------------------|-------------------------------|
| GitHub Copilot| `.github/`       | Agents, instructions, prompts, skills |
| Claude Code   | `CLAUDE.md`      | Plus `AGENTS.md` and `.agent/`|
| Other LLMs    | `AGENTS.md`      | Read `.agent/` for full context|

<!-- CUSTOM START -->
<!-- Add project-specific content below this line -->
<!-- CUSTOM END -->
