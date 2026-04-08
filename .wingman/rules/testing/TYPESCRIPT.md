---
name: TypeScript Testing Standards
description: Language-specific testing standards for TypeScript projects
category: testing
paths:
  - "**/*.ts"
  - "**/*.tsx"
  - "**/*.mts"
  - "**/*.cts"
---

# TypeScript Testing Standards

## Testing Philosophy

Follow the Testing Trophy (Kent C. Dodds): "Write tests. Not too many. Mostly integration."

1. **Static analysis** (TypeScript + ESLint) -- catches typos, type errors, style issues. Free confidence.
2. **Unit tests** -- for pure functions with complex logic. Fast, cheap, isolated.
3. **Integration tests** -- the sweet spot. Test multiple units working together. Best confidence-to-cost ratio.
4. **End-to-end tests** -- for critical user paths only. Expensive but highest confidence.

## What to Test / What Not to Test

- Test behavior, not implementation. Never test internal state, private methods, or specific event handler names.
- Test what users see, do, and experience.
- If a refactor requires rewriting tests, those tests were testing implementation details.
- Diminishing returns beyond ~70% code coverage. Do not pursue 100%.

## Test Structure

- Follow **Arrange/Act/Assert (AAA)** pattern in every test.
- One assertion concern per test. Multiple `expect` calls are fine if they verify one behavior.
- Test names must be descriptive: `it("returns 404 when user does not exist")` -- not `it("works")`.
- No logic in tests (no conditionals, no loops).
- Use `describe` blocks to group related tests.
- Use `beforeEach`/`afterEach` for setup/teardown, not shared mutable state.

## Mocking Guidelines

**Mock at boundaries, not internals.**

Mock these:
- HTTP APIs, databases, file systems
- Third-party services
- Time/dates, randomness

Do NOT mock these:
- Your own modules
- Utility functions
- Data transformations
- Anything you control

Every mock reduces integration confidence. Minimize them.

When you mock:
- Use `vi.fn()` with typed signatures so TypeScript validates argument/return types match the original.
- Periodically verify mock contracts match real service behavior.

## Vitest-Specific Practices

- Prefer `vi.fn()` and `vi.spyOn()` over manual stubs.
- Use `vi.mock()` for module mocking.
- Use `expectTypeOf` and `assertType` for compile-time type testing.

## Property-Based Testing (fast-check)

Use property-based tests for functions with complex input spaces:
- Parsers, serializers, encoders/decoders
- Sorting, mathematical operations
- Data transformations

Define properties that must hold for ALL inputs, not just specific examples:
- Round-trip: `decode(encode(x)) === x`
- Invariant: "output is always sorted"
- Idempotency: `f(f(x)) === f(x)`

fast-check automatically shrinks failing inputs to minimal counterexamples.

Property-based tests complement but do not replace example-based tests. Use both.

## Coverage Expectations

- 70%+ statement coverage as a general target.
- 90%+ on core business logic (payments, auth, data validation).
- Enable branch coverage in addition to statement coverage.
- Coverage percentage alone is misleading. Review uncovered lines to assess actual risk.
- Every bug fix should include a regression test that would have caught the bug.

## Code Review: Testing Checks

- Tests follow AAA structure.
- Test names describe the behavior being verified, not the method name.
- No logic in tests.
- Mocks are justified. No mocking of modules you own unless necessary for isolation.
- Edge cases are covered: empty input, null, boundary values, error paths.
