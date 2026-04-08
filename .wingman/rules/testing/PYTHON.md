---
name: Python Testing Standards
description: Language-specific testing standards for Python projects
category: testing
paths:
  - "**/*.py"
---

# Python Testing Standards

## Test Organization

- Mirror source directory structure in tests: `src/auth/service.py` -> `tests/auth/test_service.py`.
- Store shared fixtures in `conftest.py` at the appropriate directory level.
- Separate unit tests from integration tests: `tests/unit/`, `tests/integration/`.
- Name test files `test_*.py`, test functions `test_<method>_<scenario>`.
- Use `pytest --strict-markers` to prevent typos in custom markers from silently passing.

## Fixtures

- Use `function` scope (default) for test-specific state (temp files, isolated DB records).
- Use `module` or `session` scope for expensive shared resources (DB connections, API clients).
- Use the factory pattern when a single test needs multiple instances of a fixture.
- Use `yield` fixtures for setup/teardown. Code after `yield` handles cleanup.
- Each fixture should have a single responsibility. Compose small fixtures rather than building monolithic ones.
- Never overuse global fixtures. They cause test interdependence and cascade failures.

## Mocking

- Always patch where the object is **used**, not where it is **defined**: `@patch('mymodule.requests.get')` not `@patch('requests.get')`.
- Always use `autospec=True` when patching to enforce correct method signatures on mocks.
- Mock external boundaries (network calls, file system, third-party APIs), not internal logic.
- Prefer `pytest-mock`'s `mocker` fixture over `unittest.mock.patch` decorators.
- Do not mock what you do not own. Write adapters/wrappers around third-party code, then mock the adapter.
- Extensive mocking in a single test signals the code under test has too many dependencies and should be refactored.

## Property-Based Testing (Hypothesis)

Use Hypothesis for functions with broad input domains:
- Parsers, serializers, data transformations, math

Test properties and invariants, not specific input/output pairs:
- Round-trip: `decode(encode(x)) == x`
- Invariant: "output is always sorted"
- Preservation: "length is preserved"

Rules:
- Use `@given(st.text(), st.integers())` to generate inputs. Hypothesis finds edge cases you would miss.
- Use `assume()` to filter invalid inputs rather than raising exceptions.
- Be aware: pytest fixtures run once per test function with Hypothesis, not once per example.
- Use `--hypothesis-show-statistics` to review test generation effectiveness.

## Coverage Expectations

- Enforce minimum 80% statement coverage as a CI gate. Aim for 90%+ on core business logic.
- Enable branch coverage (`--cov-branch`) in addition to statement coverage.
- Do not chase 100% coverage at the expense of meaningful tests.
- Coverage percentage alone is misleading. Review uncovered lines to assess actual risk.
- Every bug fix should include a regression test that would have caught the bug.

## General Testing Rules

- Each test should test exactly one behavior.
- Tests must be independent and order-independent. Never rely on test execution order.
- Use descriptive test names: `test_create_user_raises_on_duplicate_email`.
- Never use external resources (real databases, real APIs) in unit tests.
- Use `pytest.raises(SpecificException)` to assert exceptions, not try/except in tests.
- Use `pytest.mark.parametrize` to test the same logic with multiple inputs.
- Use `tmp_path` fixture for temporary file operations.
- Set up async test clients from day one if using async frameworks.
- No logic in tests (no conditionals, no loops).
- Follow **Arrange/Act/Assert (AAA)** pattern in every test.

## Code Review: Testing Checks

- Tests follow AAA structure.
- Test names describe the behavior, not the method name.
- Mocks are justified and use `autospec=True`.
- Edge cases covered: empty input, None, boundary values, error paths.
- New code has corresponding tests. Critical paths have both unit and integration tests.
- No mutable defaults in fixtures.
