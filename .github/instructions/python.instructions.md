---
applyTo: "**/*.py,**/pyproject.toml,**/setup.py,**/setup.cfg,**/requirements*.txt,**/Pipfile,**/uv.lock"
---

# Python Guidelines

Python-specific syntax, idioms, and conventions. Design principles are in design.instructions.md; XP workflow in xp.instructions.md; refactoring in refactoring.instructions.md.

## Version Detection

Detect Python version before generating code:
- `pyproject.toml`: `[project] requires-python`, `[tool.poetry.dependencies] python`
- `.python-version` file
- `setup.cfg`: `python_requires`
- `Pipfile`: `[requires] python_version`
- Adapt syntax to detected version; never use features unavailable in the project's Python version

### Version-Specific Features
| Version | Key Features to Use |
|---------|---------------------|
| 3.8 | Walrus operator (`:=`), `f-strings`, `TypedDict`, `Protocol` |
| 3.9 | `dict | dict` merge, `list[int]` built-in generics (no `typing.List`) |
| 3.10 | `match` statements, `ParamSpec`, `TypeGuard`, `X | Y` union syntax |
| 3.11 | `ExceptionGroup`, `TaskGroup`, `tomllib`, `Self` type |
| 3.12 | Type parameter syntax (`def f[T](x: T)`), `override` decorator, f-string improvements |
| 3.13+ | Free-threaded mode (experimental), improved error messages |

## Package Manager: UV

Prefer **uv** as the package manager and virtual environment tool:
- `uv init` to start a new project
- `uv add <package>` to add dependencies (not `pip install`)
- `uv sync` to install from lockfile
- `uv run <command>` to run within the managed environment
- `uv venv` to create virtual environments
- Fall back to `pip` + `venv` only if `uv` is not available or the project already uses another tool
- Respect existing `pyproject.toml`, `requirements.txt`, or `Pipfile` if present

## Idiomatic Python

Write Pythonic code — clear, concise, and idiomatic to the language.

### The Zen of Python (Selected)
- Beautiful is better than ugly
- Explicit is better than implicit
- Simple is better than complex
- Readability counts
- There should be one — and preferably only one — obvious way to do it

### Pythonic Patterns
- Use list/dict/set comprehensions over `map`/`filter` when readable
- Use generators and `yield` for lazy iteration over large datasets
- Use `with` statements for resource management (files, connections, locks)
- Use `dataclasses` or `NamedTuple` for data containers; avoid raw dicts for structured data
- Use `pathlib.Path` over `os.path` for file system operations
- Use `enum.Enum` for fixed sets of constants
- Unpack tuples and sequences directly: `x, y = point`
- Use `*args` and `**kwargs` intentionally, not as escape hatches
- Prefer `collections.defaultdict`, `Counter`, `deque` over manual implementations

### What to Avoid
- Mutable default arguments (`def f(items=[])` — use `None` sentinel)
- Bare `except:` — always catch specific exceptions
- `type()` for type checking — use `isinstance()`
- String concatenation in loops — use `str.join()` or f-strings
- God modules — split large modules by responsibility

## Type Hints

- Add type hints to all function signatures
- Use built-in generics (`list[str]`, `dict[str, int]`) on Python 3.9+; `from __future__ import annotations` for 3.8
- Use `Optional[X]` or `X | None` (3.10+) for nullable types
- Use `Protocol` for structural subtyping (duck typing with type safety)
- Use `TypeVar` for generic functions; `ParamSpec` for decorator typing
- Return explicit types; avoid returning `Any`

## Style & Conventions

- PEP 8 unless project config differs (check `pyproject.toml` for tool settings)
- Snake_case for functions, methods, variables, modules
- PascalCase for classes
- SCREAMING_SNAKE_CASE for module-level constants
- Leading underscore for private/internal names (`_helper`, `_internal_method`)
- No wildcard imports (`from module import *` forbidden)
- Absolute imports preferred; relative imports only within packages
- Naming: if a name needs a comment to explain it, rename it

## Error Handling

- Raise specific exceptions with context: what failed and why
- Catch specific exceptions; avoid `except Exception` unless re-raising
- Use custom exception classes for domain-specific errors
- Use `raise ... from` to chain exceptions and preserve tracebacks
- Fail fast at function entry with guard clauses
- Use `contextlib.suppress()` for intentional exception swallowing (rare)

## Testing: pytest

Prefer **pytest** over `unittest` for all new test code:
- Use plain `assert` statements (pytest rewrites them for rich diffs)
- Use `pytest.fixture` for setup/teardown, not `setUp`/`tearDown` methods
- Use `@pytest.mark.parametrize` for data-driven tests
- Use `tmp_path` fixture for temporary files
- Use `monkeypatch` for patching; prefer it over `unittest.mock.patch` for simplicity
- Use `pytest.raises` as a context manager for expected exceptions
- Test file naming: `test_<module>.py`; test function naming: `test_<behavior>()`
- Keep tests fast; mock external dependencies with `unittest.mock.Mock` or `pytest-mock`
- Arrange-Act-Assert structure; one concept per test

If the project already uses `unittest`, match the existing style rather than mixing frameworks.

## Null Safety

- Use `Optional[T]` or `T | None` to signal nullable values
- Guard against `None` early; prefer `if value is None` over `if not value` (avoids falsy traps with `0`, `""`, `[]`)
- Use `typing.assert_never()` for exhaustive match checking (3.11+)

## Code Quality Tooling (Optional)

These tools are **not required** but can significantly enhance code review and expert panel analysis by producing actionable reports. Adding them is an opt-in decision — when prompted, offer to configure them.

When a user asks for code review, expert panel review, or static analysis insights, suggest:
*"This project doesn't have static analysis or coverage reporting configured. Would you like me to add ruff, mypy, and pytest-cov? They produce reports that improve review quality without breaking the build."*

### Recommended Tools
| Tool | Purpose | Config |
|------|---------|--------|
| **ruff** | Linting + formatting (replaces flake8, isort, black) | `[tool.ruff]` in `pyproject.toml` |
| **mypy** | Static type checking | `[tool.mypy]` in `pyproject.toml` |
| **pytest-cov** | Test coverage reporting | `--cov` flag or `[tool.pytest.ini_options]` |

### Configuration Principles
- All config goes in `pyproject.toml` (single source of truth)
- **Non-breaking**: tools should warn, not fail the build; use `--exit-zero` for CI or report-only modes
- Reports feed into code review: coverage gaps inform test suggestions, type errors surface design issues, lint violations flag code smells
- Start permissive, tighten incrementally — don't dump 500 errors on an existing codebase

### Example pyproject.toml Sections

```toml
[tool.ruff]
target-version = "py312"  # match project's Python version
line-length = 120

[tool.ruff.lint]
select = ["E", "F", "I", "N", "W", "UP", "B", "SIM", "RUF"]

[tool.mypy]
python_version = "3.12"
warn_return_any = true
warn_unused_configs = true
ignore_missing_imports = true  # start permissive

[tool.pytest.ini_options]
addopts = "--cov=src --cov-report=term-missing --cov-report=html"
```

Reference: Hettinger's talks on "Transforming Code into Beautiful, Idiomatic Python"; Ramalho's *Fluent Python*
