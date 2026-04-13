---
name: 'Python Conventions'
description: 'Python coding standards'
applyTo: '**/*.py'
---

# Python Conventions

- Python 3.10+ — use modern syntax (match/case, type unions with `|`)
- Type hints on all function signatures
- Docstrings on all public functions/classes (Google style)
- `pathlib.Path` over `os.path`
- Prefer `dataclass` or `pydantic.BaseModel` for data structures
- Specific exception handling — no bare `except:`
- Format with `ruff`; lint with `ruff`
- Tests in `tests/` using `pytest`

Refer to [.agent/context/conventions.md](../../.agent/context/conventions.md) for project-specific patterns.
