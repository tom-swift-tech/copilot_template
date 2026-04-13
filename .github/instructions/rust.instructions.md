---
name: 'Rust Conventions'
description: 'Rust coding standards and patterns'
applyTo: '**/*.rs'
---

# Rust Conventions

- Use `thiserror` for library errors, `anyhow` only in binary crates
- `#[derive(Debug, Clone, Serialize, Deserialize)]` on all public types
- Prefer newtypes over raw primitives for IDs (`UserId(u64)`, not `u64`)
- Doc comments (`///`) on all public items
- Tests in inline `#[cfg(test)] mod tests` blocks, not separate files
- Prefer exhaustive `match` — avoid catch-all `_` for enums in game/business logic
- No `unwrap()` in library code — use proper `Result<T, E>` handling
- Use `BTreeMap` when iteration order matters for determinism

Refer to [.agent/context/conventions.md](../../.agent/context/conventions.md) for project-specific patterns.
