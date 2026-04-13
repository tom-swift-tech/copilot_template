# CLAUDE.md

> Claude Code auto-loads this file. For GitHub Copilot, see `.github/copilot-instructions.md`.

## Purpose

Enterprise-ready template for AI-assisted development using the Folder Agent pattern — the folder structure IS the agent. Ships with GitHub Copilot agents, Claude Code context, reusable prompts, language-specific instructions, and a structured knowledge base. Designed for teams adopting AI coding tools at FNF and beyond.

## Architecture

Two-layer design:

- **`.github/`** — GitHub Copilot-native primitives (agents, instructions, prompts, skills)
- **`.agent/`** — LLM-agnostic knowledge base (context, tasks, memory)

Both layers are active. When using Claude Code, read from both.

Five functional agents with model routing: Architect (Opus/GPT-5.2), Analyst (Opus/GPT-5.2), Scaffolder (Codex/Sonnet), Builder (Codex/Sonnet), Reviewer (Opus/Sonnet). See `AGENTS.md` for full roster, org rules, and handoff protocol. Analyst is a **gate, not a step** — pressure-tests designs and ADRs, returns findings to Architect, never proposes alternatives.

## Conventions

- Default to Builder mode. Announce mode switches explicitly.
- Read `.agent/context/` and `.agent/memory/gotchas.md` before making changes.
- Language-specific rules in `.github/instructions/` — load based on file type being edited.
- Context loading order: CLAUDE.md → AGENTS.md → project context → conventions → infrastructure → stack → decisions → tasks → gotchas.
- After completing significant work, update memory (lessons, gotchas, patterns).
- Never commit secrets. Use Key Vault refs, env vars, or `.env.example`.

## State

Template published at `tom-swift-tech/copilot_template`. Active use for FNF Copilot enablement and training delivery. Agent model routing updated to reflect current model landscape (Opus 4.6, Sonnet 4.6, Codex GPT-5.3, GPT-5.2). Org rules formalized: no agent does two jobs, reviewer never reviews its own work, architect never writes code. Handoff protocol defined. This template is the origin of the Rig Pattern.

**Test and Validate baked in (2026-04-13):** Every workflow prompt and every agent handoff ends with a mandatory `Test and Validate` step. Engineers don't need to remember it — the prompts and agent rules enforce it. ADR and design doc templates require Validation / Test Plan sections. Reviewer rejects any work without test evidence.

**Drift prevention:** The `sync-agents` skill (`.github/skills/sync-agents/`) verifies the agent roster is consistent across `.github/agents/`, `AGENTS.md`, and `.github/copilot-instructions.md`. Run it after any change to those files. Current canonical set: 5 agents (architect, analyst, scaffolder, builder, reviewer).

**Day 1 onboarding:** [docs/GETTING-STARTED.md](docs/GETTING-STARTED.md) is the kickoff runbook for new projects.
