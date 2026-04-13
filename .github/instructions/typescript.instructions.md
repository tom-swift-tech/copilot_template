---
name: 'TypeScript Conventions'
description: 'TypeScript and React coding standards'
applyTo: '**/*.{ts,tsx}'
---

# TypeScript Conventions

- Strict mode — no `any` without justification
- Named exports over default exports
- Interfaces for object shapes, types for unions/intersections
- Async/await over raw promises
- Prefer `const`; never use `var`
- Error handling: typed errors, no bare `catch(e)`
- Group imports: external → internal → relative

## React (if applicable)

- Functional components with hooks — no class components
- Props interfaces: `{ComponentName}Props`
- Custom hooks prefixed with `use`

Refer to [.agent/context/conventions.md](../../.agent/context/conventions.md) for project-specific patterns.
