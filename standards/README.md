# Extended Standards

Project-level standards that go deeper than what fits in `.github/copilot-instructions.md`.

Every standard in this directory is a **starter** — concise, opinionated, and meant to be replaced or expanded for your project. Each ends with a mandatory **Test and Validate** section so engineers know how to prove the standard was actually followed.

## Index

| Standard | What it covers | When to read it |
|----------|----------------|-----------------|
| [API-DESIGN.md](API-DESIGN.md) | URL/verb conventions, versioning, errors, pagination, auth | Building or changing an HTTP API |
| [DATABASE-DESIGN.md](DATABASE-DESIGN.md) | Schema, constraints, indexes, migrations, query safety | Touching schema or writing queries |
| [OBSERVABILITY.md](OBSERVABILITY.md) | Logging, metrics, traces, alerting, dashboards | Adding any user-visible service feature |
| [DEPLOYMENT.md](DEPLOYMENT.md) | Release strategies, pre/post-deploy gates, rollback | Anything that ships to a shared environment |
| [SECURITY.md](SECURITY.md) | Auth, authz, secrets, input validation, dependencies | Always — security is cross-cutting |

## How agents use these

Agents do **not** auto-load standards docs on every request — they're too large and most aren't relevant to a given task. Instead:

- **Builder** reads the relevant standard when starting work on a topic that touches it (e.g., `DATABASE-DESIGN.md` before writing a migration).
- **Reviewer** consults the relevant standards when reviewing changes in scope (e.g., `SECURITY.md` for any auth-touching PR).
- **Architect** references the standards in design docs to make conformance an explicit design constraint.

If you want a standard auto-loaded for a specific file pattern, create a corresponding `.github/instructions/*.instructions.md` with an `applyTo` glob and link to the standard.

## Adding a new standard

1. Create `<NAME>.md` in this directory.
2. Match the format of an existing standard: scope → conventions → **Test and Validate** (mandatory).
3. Add a row to the index table above.
4. If the standard implies a hard rule, also add the one-line version to `.agent/context/conventions.md` so it's auto-loaded.
