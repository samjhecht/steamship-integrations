---
name: Python Coding Standards
description: Language-specific coding standards for Python projects
category: coding
paths:
  - "**/*.py"
---

# Python Coding Standards

## Imports

- Use `import x` for packages/modules only, never for individual classes or functions.
- Never use relative imports. Always use the full package path.
- All imports at the top of the file, after module docstring, before globals/constants.
- Group imports in order: (1) `__future__`, (2) stdlib, (3) third-party, (4) local/project. Sort lexicographically within groups.
- Each import on its own line (exception: `typing` and `collections.abc` may have multiple symbols per line).
- Place type-only imports inside `if TYPE_CHECKING:` block.

## Type Hints

- Always use type hints on all public function signatures (parameters and return types).
- Use built-in generics (`list[int]`, `dict[str, int]`) on Python 3.9+. Never use `typing.List`, `typing.Dict`.
- Prefer `collections.abc.Sequence` over `list` and `collections.abc.Mapping` over `dict` in function parameters.
- Always annotate `None` possibility explicitly: use `X | None` or `Optional[X]`. Never write `a: str = None` without the union annotation.
- Always specify type parameters for generic types. Never leave them to default to `Any`.
- Type aliases use `CapWords` naming and should be annotated with `: TypeAlias` on Python 3.10+.

## Naming Conventions

| Entity | Convention | Example |
|--------|-----------|---------|
| Modules | `lower_with_under.py` | `user_service.py` |
| Classes | `CapWords` | `UserService` |
| Internal classes | `_CapWords` | `_InternalHelper` |
| Functions/methods | `lower_with_under()` | `get_user()` |
| Constants | `CAPS_WITH_UNDER` | `MAX_RETRIES` |
| Variables | `lower_with_under` | `user_count` |
| Exception classes | `CapWords` ending in `Error` | `NotFoundError` |

- Do not abbreviate by deleting letters within words.
- Do not include type in name (avoid `id_to_name_dict`).
- Test methods: `test_<method_under_test>_<state>`.

## Docstrings

- Always use triple double-quotes `"""` for all docstrings.
- Every module must have a docstring.
- Every public function must have a docstring with enough info to call it without reading the code.
- Document parameters in `Args:` section, return values in `Returns:`, exceptions in `Raises:`.
- Overridden methods decorated with `@override` do not require docstrings unless behavior materially changes.

## Functions and Classes

- Keep functions under ~40 lines. If longer, consider splitting.
- Never use mutable objects as default arguments. Use `None` and initialize inside: `def foo(items=None): if items is None: items = []`.
- Never use `@staticmethod`. Write a module-level function instead.
- Use `@classmethod` only for named constructors.
- Use `@override` decorator on overridden methods.

## Comprehensions and Expressions

- Comprehensions allowed but never with multiple `for` clauses or filter expressions.
- Prefer generator expressions over `map()`/`filter()` with lambda.
- Lambda allowed for one-liners only.

## Boolean/None Evaluation

- Use implicit false for sequences: `if seq:` not `if len(seq) > 0:`.
- Always check None explicitly: `if x is None:` never `if not x:` when testing for None.
- Never compare boolean to `False` with `==`. Use `if not x:`.
- For integers, compare against explicit value: `if count == 0:` not `if not count:`.

## Formatting

- Use Ruff as the single tool for both linting and formatting.
- Maximum line length: 88 characters (Ruff/Black default).
- 4-space indentation, never tabs.
- Two blank lines between top-level definitions. One between methods.
- Trailing commas on multi-line collections.
- Never use backslash line continuation. Use implicit continuation within parentheses.

## Strings and Logging

- Use f-strings for string formatting in application code.
- Never accumulate strings with `+`/`+=` in loops. Use `list.append()` then `''.join()`.
- For logging, use `%`-style pattern strings: `logger.warning("User %s not found", user_id)`. Avoids rendering messages that are never output and preserves queryable patterns.

## FastAPI/Pydantic Patterns

- Use Pydantic models for all request/response validation. Never trust raw input.
- Use `Field(min_length=1, max_length=128)` for string constraints, `Field(ge=0, le=100)` for numeric constraints.
- Use `async def` for I/O-bound routes, `def` for CPU-bound (FastAPI runs sync routes in threadpool).
- Never use `time.sleep()` in async routes. Use `await asyncio.sleep()`.
- Return `response_model` on every route.
- Split `BaseSettings` config across modules rather than one monolith.

## Global State and Resources

- Avoid mutable global state.
- Module-level constants are fine, using `CAPS_WITH_UNDER`.
- Always use `with` statements for resource management (files, sockets, connections).
- Every executable file must have `if __name__ == '__main__':` guard.
