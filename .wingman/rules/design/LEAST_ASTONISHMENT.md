---
name: Principle of Least Astonishment
description: Interfaces, APIs, and behaviors should act the way users and callers reasonably expect -- surprises are bugs
category: design
---

# Principle of Least Astonishment (POLA)

A component should behave the way its users -- whether humans or calling code -- would reasonably expect. When there is a conflict between what is "correct" and what is expected, the expected behavior usually wins.

## What Counts as Surprising

- A function named `getUser` that silently creates a user when one isn't found.
- A `delete` operation that archives instead of deleting (or vice versa) with no indication.
- A `save()` method that also sends a notification email.
- A config flag where `true` disables a feature.
- An API that returns `200 OK` for failed operations with an error buried in the body.
- Sort functions that mutate the original collection in a language where most operations return new values (or vice versa).

## How to Apply

**Naming**: A function's name is a promise. `validate` should not mutate. `find` should not create. `get` should not throw. If the behavior doesn't match a plain reading of the name, rename it or split it.

**Conventions over cleverness**: Follow the conventions of your language, framework, and codebase. A Python method returning `None` to signal "not found" is expected; raising an exception for the same case is surprising (unless the method is named `get_or_raise`). A JavaScript `.map()` that mutates in place would violate every JS developer's expectations.

**Side effects**: Operations should do what they advertise and nothing more. If a side effect is necessary, make it explicit in the name (`saveAndNotify`) or the signature (accept a callback/event emitter).

**Defaults**: Default values should be the safe, common-case choice. Destructive operations should not be the default. Opt-in to danger, not opt-out.

**Error behavior**: Errors should be proportional and predictable. Invalid input returns a 400, not a 500. A missing optional field is silently ignored, not a hard failure. An empty collection returns an empty result, not an error.

## When Expectations Conflict

Different audiences have different expectations. An end-user expects "delete" to be recoverable; a developer calling `DROP TABLE` expects permanence. Know your audience:

- **Public APIs**: Follow HTTP, REST, and ecosystem conventions. New users will assume standard behavior.
- **Internal APIs**: Follow your codebase's established patterns. Consistency within the project trumps external convention.
- **CLIs**: Destructive commands should require confirmation or a `--force` flag. Dry-run should be the default for dangerous operations.

## Relationship to Other Rules

- **Command Query Separation**: A query that mutates is a POLA violation by definition.
- **Idempotency**: Users expect that retrying a failed request won't double-charge them. Non-idempotent POST operations surprise callers in failure scenarios.
- **Parse Don't Validate**: An API that silently coerces invalid input into something "close enough" surprises callers who expected rejection.
