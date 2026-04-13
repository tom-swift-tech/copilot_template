# CLAUDE.md

> Claude Code auto-loads this file. For GitHub Copilot, see `.github/copilot-instructions.md`.

## Purpose

Enterprise-ready template for AI-assisted development using the Folder Agent pattern — the folder structure IS the agent. Ships with GitHub Copilot agents, Claude Code context, reusable prompts, language-specific instructions, and a structured knowledge base. Designed for teams adopting AI coding tools at FNF and beyond.

## Architecture

Two-layer design:

- **`.github/`** — GitHub Copilot-native primitives (agents, instructions, prompts, skills)
- **`.agent/`** — LLM-agnostic knowledge base (context, tasks, memory)

Both layers are active. When using Claude Code, read from both.

Four functional agents with model routing: Architect (Opus/GPT-5.2), Scaffolder (Codex/Sonnet), Builder (Codex/Sonnet), Reviewer (Opus/Sonnet). See `AGENTS.md` for full roster, org rules, and handoff protocol.

## Conventions

- Default to Builder mode. Announce mode switches explicitly.
- Read `.agent/context/` and `.agent/memory/gotchas.md` before making changes.
- Language-specific rules in `.github/instructions/` — load based on file type being edited.
- Context loading order: CLAUDE.md → AGENTS.md → project context → conventions → infrastructure → stack → decisions → tasks → gotchas.
- After completing significant work, update memory (lessons, gotchas, patterns).
- Never commit secrets. Use Key Vault refs, env vars, or `.env.example`.

## State

Template published at `tom-swift-tech/copilot_template`. Active use for FNF Copilot enablement and training delivery. Agent model routing updated to reflect current model landscape (Opus 4.6, Sonnet 4.6, Codex GPT-5.3, GPT-5.2). Org rules formalized: no agent does two jobs, reviewer never reviews its own work, architect never writes code. Handoff protocol defined. This template is the origin of the Rig Pattern.
