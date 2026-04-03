# Conventions

> Project-level coding standards. Language-specific rules live in `.github/instructions/*.instructions.md`.
> This file covers cross-language conventions enforced by all agents.

---

## Naming

### Files and Directories

| Convention         | Example                    | Used For                     |
|--------------------|----------------------------|------------------------------|
| `kebab-case`       | `user-service.ts`          | TypeScript/JS files, folders |
| `snake_case`       | `user_service.rs`          | Rust files                   |
| `snake_case`       | `user_service.py`          | Python files                 |
| `PascalCase`       | `UserService.cs`           | C# files                     |
| `PascalCase`       | `UserService.ps1`          | PowerShell (Verb-Noun)       |
| `kebab-case`       | `deploy-webapp.yml`        | Ansible playbooks, YAML      |
| `snake_case`       | `main.tf`                  | Terraform files              |

### Code Identifiers

| Language    | Variables / Functions | Types / Classes | Constants        |
|-------------|----------------------|-----------------|------------------|
| TypeScript  | `camelCase`          | `PascalCase`    | `SCREAMING_SNAKE`|
| Rust        | `snake_case`         | `PascalCase`    | `SCREAMING_SNAKE`|
| Python      | `snake_case`         | `PascalCase`    | `SCREAMING_SNAKE`|
| PowerShell  | `PascalCase`         | `PascalCase`    | `$SCREAMING_SNAKE`|

### Branch Names

`{type}/{ticket-or-slug}` — e.g., `feature/valor-mission-routing`, `fix/herd-timeout-bug`, `chore/update-deps`

### Commit Messages

Conventional Commits format:

```
<type>(<scope>): <description>

[optional body]

[optional footer(s)]
```

Types: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `chore`, `infra`, `ci`

---

## Error Handling

### General Principles

1. **Fail fast**: Validate inputs at boundaries; don't propagate bad data.
2. **Typed errors**: Use language-appropriate error types (Rust `Result<T, E>`, TypeScript custom `Error` subclasses, Python exception hierarchy).
3. **No silent swallows**: Every `catch` / `except` / `Err` must log, re-raise, or return a meaningful error.
4. **Context enrichment**: Wrap lower-level errors with context about what operation was attempted.
5. **User-facing vs internal**: Never expose stack traces or internal paths to end users.

### By Language

| Language    | Pattern                                                         |
|-------------|-----------------------------------------------------------------|
| Rust        | `Result<T, E>` everywhere; `?` operator; `thiserror` for libs, `anyhow` for bins; **no `unwrap()` in library code** |
| TypeScript  | Custom error classes extending `Error`; `try/catch` at boundaries; typed error returns for internal APIs |
| Python      | Custom exception hierarchy; `try/except` with specific exceptions; never bare `except:` |
| PowerShell  | `try/catch` with `Write-Error`; `$ErrorActionPreference = 'Stop'` in scripts |

---

## Testing

### Test Pyramid

```
        ╱ E2E ╲            Fewest — slow, expensive, high confidence
       ╱ Integration ╲      Moderate — test boundaries and contracts
      ╱   Unit Tests   ╲    Most — fast, isolated, cheap to run
```

### Requirements

- **Coverage**: Aim for 80%+ on critical paths. Don't chase 100% — diminishing returns.
- **Naming**: `test_<what>_<condition>_<expected>` (e.g., `test_parse_config_missing_field_returns_error`).
- **No test interdependence**: Each test sets up and tears down its own state.
- **Mock external dependencies**: Network calls, databases, file systems — mock at the boundary.
- **CI gate**: Tests must pass before merge. No exceptions.

### By Language

| Language    | Framework      | Command              |
|-------------|----------------|----------------------|
| Rust        | Built-in       | `cargo test`         |
| TypeScript  | Vitest / Jest  | `npm test`           |
| Python      | pytest         | `pytest -v`          |
| PowerShell  | Pester         | `Invoke-Pester`      |
| Terraform   | `terraform validate` + `checkov` | `.vscode/tasks.json` |

---

## Dependencies

### Adding Dependencies

1. **Justify**: Every new dependency must solve a real problem that doesn't warrant a custom solution.
2. **Evaluate**: Check maintenance status, license compatibility (MIT/Apache preferred), security advisories.
3. **Pin versions**: Use lockfiles (`Cargo.lock`, `package-lock.json`, `requirements.txt` with hashes).
4. **Minimal surface**: Prefer libraries that do one thing well over kitchen-sink frameworks.

### Updating Dependencies

- **Automated scanning**: Dependabot / Renovate for PRs on security patches.
- **Major version bumps**: Manual review, test in `dev` first, document breaking changes.
- **Audit trail**: Dependency changes get their own commit, not mixed with feature work.

---

## Code Review

### Reviewer Checklist

1. ✅ Does it do what the ticket/issue describes?
2. ✅ Are edge cases handled (null, empty, overflow, timeout)?
3. ✅ Is error handling complete (no silent failures)?
4. ✅ Are tests added or updated for the change?
5. ✅ Does naming follow conventions?
6. ✅ Is there no hardcoded config that should be a variable?
7. ✅ Are secrets properly managed (no credentials in code)?
8. ✅ Is documentation updated if behavior changed?

### Anti-Patterns to Flag

- `// TODO` or `// FIXME` without a linked issue
- Magic numbers / strings without named constants
- Copy-pasted code blocks (extract to function/module)
- Deeply nested conditionals (refactor to early returns / guard clauses)
- Overly broad error catches (`catch (e) {}`, bare `except:`)
- Console logging left in production code paths
- Missing input validation at API boundaries
- Terraform `count` where `for_each` would be more readable
