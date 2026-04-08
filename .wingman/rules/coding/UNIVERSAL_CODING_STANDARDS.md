---
name: Language-Agnostic Coding Principles
description:
category: coding
---

# Language-Agnostic Coding Principles

## General

- NEVER remove or downgrade code to fix type errors from outdated dependencies; upgrade the dependency instead
- Always ask before removing functionality or code that appears to be intentional
- Remove comments that restate the code — explain WHY, not WHAT — Code should be self-documenting through naming and structure; comments add value by explaining rationale, non-obvious behavior, or design decisions that aren't evident from the implementation itself
- Simplify nested conditionals: use and/or for compound conditions, elif for mutual exclusion — Reduces nesting and cognitive load, making control flow easier to understand and maintain
- Extract shared logic into helper methods instead of duplicating inline — especially between method variants like streaming/non-streaming — Reduces maintenance burden and prevents divergence bugs when one copy is updated but others aren't
- Remove unreachable code branches—let impossible cases fail explicitly rather than silently handle them — Eliminates dead code that obscures logic and prevents detection of actual bugs when "impossible" conditions do occur
- Eliminate duplicate validation logic — extract repeated checks into shared helpers or reuse existing parent/utility validations — Prevents inconsistencies when validation logic changes and reduces maintenance burden by keeping validation logic in one place (DRY principle)

## Error Handling

- Never swallow exceptions silently. Empty catch blocks hide bugs. At minimum, log the error.
- Fail fast, fail loudly. Validate inputs at boundaries and throw immediately on invalid state.
- Use typed/specific errors over generic ones. Create domain-specific error types.
- Separate user-facing messages from internal details. Log full stack traces; show users a generic message with a correlation ID.
- Graceful degradation: if a non-critical subsystem fails, the rest of the application should continue.

## Logging

- Use structured logging (JSON format). Fields: timestamp, level, message, service name, correlation/request ID, relevant context.
- Standard log levels: DEBUG (development), INFO (normal ops), WARN (recoverable), ERROR (failures needing attention), FATAL (system-stopping).
- Never log sensitive data: passwords, tokens, SSNs, credit card numbers, PII.
- Include correlation IDs: every request gets a unique ID that propagates through all downstream calls.
- Log the WHY, not just the WHAT: `"Payment failed: insufficient funds for user_id=123, amount=500"` beats `"Error in processPayment"`.

## Configuration Management

- Never hardcode configuration values (URLs, ports, credentials, feature toggles) in source code.
- Use environment variables for deployment-specific config (12-factor app methodology).
- Layer configuration: defaults in code, overrides from config files, final overrides from environment variables.
- Validate configuration at startup. Fail fast if required config is missing.
- Separate secrets from configuration. Use dedicated secret managers.

## Code Quality

- Functions do one thing. If a function needs an "and" in its description, split it.
- Prefer pure functions where possible. Isolate side effects.
- No magic strings or numbers. Extract to named constants.
- Prefer composition over inheritance.
- Minimize the scope of variables. Declare variables close to where they are used.
- Minimize mutable state. Use immutable data structures by default.
- Fail explicitly rather than silently. Never return null/None/undefined when an error has occurred.
