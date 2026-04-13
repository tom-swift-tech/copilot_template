# Getting Started

> Day 1 runbook for a new project using this template. Aim: be productive inside an hour.

---

## Why this template exists (60-second read — skip if you're in a hurry)

This is a **Folder Agent** template: agent behavior is encoded in folder structure and markdown front-matter, not in code. There is no framework to install, no orchestrator to run. Any LLM that can read files (GitHub Copilot, Claude Code, Cursor, Codex) inherits the same agents, the same conventions, and the same memory.

Two layers, both active at once:

- **`.github/`** — GitHub Copilot-native primitives (agents, instructions, prompts, skills)
- **`.agent/`** — LLM-agnostic knowledge base (context, tasks, memory)

Five agents with strict boundaries: **Architect** designs, **Analyst** pressure-tests the design, **Scaffolder** structures, **Builder** implements, **Reviewer** validates. No agent does two jobs. Reviewer never reviews its own work. Analyst never proposes alternatives.

**One non-negotiable rule across every workflow: every step ends with Test and Validate.** Engineers don't have to remember it because the prompts won't let them forget it.

For the deeper rationale, see [README.md](../README.md) and [docs/adr/0001-use-folder-agent-pattern.md](adr/0001-use-folder-agent-pattern.md).

---

## Day 1 — get to a working setup

### Step 1: Clone and orient (5 minutes)

```bash
git clone <this-template> my-project
cd my-project
```

Read these in order. Don't skim — they're short on purpose:

1. [README.md](../README.md) — what the template ships with
2. [AGENTS.md](../AGENTS.md) — the four agents and their boundaries
3. [.agent/context/conventions.md](../.agent/context/conventions.md) — the cross-language coding rules every agent will enforce

### Step 2: Fill in your project (15 minutes)

These files ship as templates with placeholder fields. Fill them now — agents read them on every chat request, and stale placeholders will pollute every response.

| File | What to fill |
|------|--------------|
| [.agent/context/project.md](../.agent/context/project.md) | Project name, one-line description, repo URL, primary architecture style, environments, on-call/contacts |
| [.agent/context/stack.md](../.agent/context/stack.md) | Languages, frameworks, runtime versions, infrastructure components, key external services |
| [.agent/context/decisions.md](../.agent/context/decisions.md) | Leave empty for now — your first ADR (Step 4) will land here |

### Step 3: Pick your language instructions (2 minutes)

Open [.github/instructions/](../.github/instructions/) and **delete** any `*.instructions.md` files for languages your project doesn't use. Each one auto-loads when an agent edits a matching file — keeping unused ones around adds noise and risks inconsistent rules.

The shipped languages: TypeScript, Python, Rust, Terraform, Ansible, PowerShell, Docs.

### Step 4: Write your first ADR (15 minutes)

The first ADR is the most important one — it documents *why your project exists*. Use the template:

```bash
cp docs/adr/TEMPLATE.md docs/adr/0001-project-charter.md
```

Fill in: Context (what problem the project solves), Decision (what you're building), Consequences, and the **Validation** section (how you'll know if you got it right).

Then add it to [.agent/context/decisions.md](../.agent/context/decisions.md) as a one-line index entry.

### Step 5: Create your first task (5 minutes)

Open [.agent/tasks/current.md](../.agent/tasks/current.md). Replace the commented template block with a real task — typically the first thing you want to build. Fill in:

- Acceptance criteria (concrete, checkable)
- The branch you'll work on
- Notes / links

Builder will read this file on every change and tick off criteria as it goes.

### Step 6: Sanity check the agent setup (3 minutes)

Open VS Code with GitHub Copilot enabled. Confirm:

- [ ] The agent dropdown in Copilot Chat shows Architect, Analyst, Scaffolder, Builder, Reviewer
- [ ] Slash commands work: type `/status` in Copilot Chat — you should get a project dashboard
- [ ] (Optional, for Claude Code) `CLAUDE.md` and `AGENTS.md` are auto-loaded on session start

If any of these fail, the template's auto-discovery isn't working. Don't push through — fix it now. The template only delivers value if the agents can find their context.

### Step 7: Run the drift check (2 minutes)

Invoke the `sync-agents` skill (from `.github/skills/sync-agents/SKILL.md`). It should report 🟢 in sync — 5 canonical agents (architect, analyst, scaffolder, builder, reviewer) matching across all three sources. If you customized any agent in Step 2 or Step 3 and it now reports 🔴, hand-correct the drift before you ship anything else.

### Step 8: Test and Validate the kickoff (mandatory — do not skip)

> Before you say "I'm set up," prove it. Setup that isn't validated is setup that quietly broke and you'll find out at the worst moment.

1. **`/status` returns a real dashboard** — not the template placeholder. If it says "no current task," you skipped Step 5.
2. **`project.md` and `stack.md` contain zero `{placeholder}` markers.** Grep for `{` in `.agent/context/`. Every match must be intentional.
3. **`docs/adr/0001-project-charter.md` exists** and has a non-empty Validation section.
4. **`sync-agents` reports 🟢** — see Step 7. Capture the output.
5. **Agent boundary smoke test (Builder):** ask Builder to "design the auth system." Builder should refuse and hand off to Architect. If it just starts designing, the agent boundaries aren't loading — investigate before doing real work.
6. **Agent boundary smoke test (Analyst):** ask Analyst to "redesign the auth system to use OAuth instead." Analyst should refuse — proposing alternatives is Architect's job. Analyst's role is critique, never proposals. If Analyst starts redesigning, the boundary isn't loading.
7. **Test and Validate gate smoke test:** ask Builder to "make a trivial change with no tests." Builder should refuse to mark it complete without a test. If it doesn't, the prompt updates didn't load — re-check `.github/prompts/feature.prompt.md`.

If all seven pass, you're done. Open your first feature with `/feature`.

---

## Day 2 and beyond — the working loop

Once setup is validated, the day-to-day rhythm depends on the change. Two common shapes:

**For bug fixes and small features (Builder-only):**

```
/feature  →  Builder implements  →  Test and Validate  →  /review  →  Reviewer approves  →  merge
                                                                            │
                                                            (or Request Changes — back to Builder)
```

**For new designs and ADRs (full pipeline):**

```
Architect drafts design  →  /pressure-test  →  Analyst critiques  →  back to Architect
                                                                          │
                                              (Architect responds to each 🔴/🟡 finding)
                                                                          ▼
                                            Scaffolder → Builder → Reviewer → merge
```

Other commands you'll use:

- **`/debug`** — structured debugging with regression-test-first
- **`/refactor`** — safe behavior-preserving changes with baseline → test → diff-check coverage
- **`/pressure-test`** — Analyst critique of a design doc or ADR (use before scaffolding non-trivial work)
- **`/test`** — focused test writing with mutation checks
- **`/deploy`** — pre-deploy checklist + post-deploy validation
- **`/status`** — current task, backlog, recent lessons, active gotchas
- **`/update-memory`** — capture session learnings into `.agent/memory/`

After each meaningful piece of work, run **`/update-memory`**. The memory files (`gotchas.md`, `lessons.md`, `patterns.md`) are the template's compounding asset — they get smarter the more honest entries you put in them.

---

## Common pitfalls on Day 1

- **Leaving placeholders in `project.md`.** Agents read this on every request. Placeholders become the project's de facto identity until you fix them.
- **Skipping the language-instructions cleanup.** Stale instructions for languages you don't use will be auto-loaded when filenames coincidentally match — leading to advice from the wrong rulebook.
- **Filling `gotchas.md` and `lessons.md` with theoretical content.** These files are meant to compound from *real* incidents. If you seed them with hypotheticals, you erode signal-to-noise from the start. Leave them empty until you have something real.
- **Treating `current.md` as a backlog.** It's intentionally limited to 3–5 items. Anything else goes in `backlog.md`.
- **Skipping the Step 8 smoke tests.** If the agent boundaries aren't loading, you'll get bad work for days before noticing. Five minutes of validation now saves a week of debugging later.

---

## Where to go next

- **Add a project-specific skill**: see [.github/skills/update-readme/SKILL.md](../.github/skills/update-readme/SKILL.md) and [.github/skills/sync-agents/SKILL.md](../.github/skills/sync-agents/SKILL.md) for the pattern
- **Add a custom slash command**: drop a `*.prompt.md` in `.github/prompts/` with front-matter
- **Add a new agent**: drop a `*.agent.md` in `.github/agents/`, add it to `AGENTS.md` and `.github/copilot-instructions.md`, then run `sync-agents` to confirm consistency. Update the org chart and roster table in `AGENTS.md` and the persona table in `README.md`.
- **Expand standards**: see `standards/` — start with the ones that matter most for your project
