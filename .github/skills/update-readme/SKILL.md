---
name: update-readme
description: "Auto-update the project README.md to reflect current structure, agents, and slash commands"
---

# Update README Skill

When invoked, regenerate the project README.md to accurately reflect the current state of the repository.

## Steps

1. **Scan structure**: Read the directory tree to identify all agents, instructions, prompts, and context files.
2. **Extract metadata**: Parse front matter from `.agent.md`, `.instructions.md`, `.prompt.md`, and `SKILL.md` files.
3. **Build sections**:
   - Project description and quick start
   - Architecture overview (two-layer: `.github/` Copilot-native + `.agent/` LLM-agnostic)
   - Agent personas table (name, mode, capabilities, tool restrictions)
   - Slash commands table (command, description, file)
   - Language instructions table (language, file pattern, file)
   - Context files inventory
   - Task and memory structure
   - VS Code tasks reference
4. **Preserve custom content**: If README.md contains a `<!-- CUSTOM START -->` / `<!-- CUSTOM END -->` block, keep it intact.
5. **Write**: Output the updated README.md.

## README Template

```markdown
# {Project Name}

> {One-line description}

## Quick Start

1. Clone the repo
2. Open in VS Code with GitHub Copilot enabled
3. Select an agent from the Copilot agent dropdown (Builder is default)
4. Use slash commands: `/debug`, `/feature`, `/review`, etc.

## Architecture

Two-layer Folder Agent pattern:

- **`.github/`** — Copilot-native primitives (auto-discovered by GitHub Copilot)
  - `agents/` — Agent personas with tool restrictions and handoffs
  - `instructions/` — Scoped coding standards by file pattern
  - `prompts/` — Slash commands for common workflows
  - `skills/` — Reusable multi-step capabilities
- **`.agent/`** — LLM-agnostic knowledge base (shared with Claude Code)
  - `context/` — Project details, conventions, stack, infrastructure, decisions
  - `tasks/` — Current work, backlog, and completed archive
  - `memory/` — Lessons, gotchas, and patterns

## Agents

| Agent       | Mode       | Access      | Purpose                              |
|-------------|-----------|-------------|--------------------------------------|
{agent_table}

## Slash Commands

| Command     | Description                                        |
|-------------|---------------------------------------------------|
{prompt_table}

## Language Instructions

| Language    | File Pattern            | File                              |
|-------------|------------------------|-----------------------------------|
{instruction_table}

## Context Files

| File                    | Purpose                                    |
|-------------------------|-------------------------------------------|
{context_table}

<!-- CUSTOM START -->
<!-- Add project-specific content here — it will be preserved on regeneration -->
<!-- CUSTOM END -->
```

## Invocation

This skill is designed to be called by agents or via a custom VS Code task.
Copilot agents can reference it in their instructions to auto-update README after structural changes.
