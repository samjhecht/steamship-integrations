---
name: README-Driven Development
description: Write the README before writing code -- use documentation as a design tool and forcing function
category: documentation
---

# README-Driven Development

Write the README before writing any code. The README is both the design document and the first deliverable. Implementation follows the documented interface, not the other way around.

## The Principle

"A perfect implementation of the wrong specification is worthless." -- Tom Preston-Werner

The README is the single most important document in a codebase. If a feature is not documented, it does not exist. If a feature is documented incorrectly, it is broken.

## Workflow

1. **Write the README first.** Describe what the project does, how to install it, and how to use it. Include realistic code examples showing the intended API or CLI interface.
2. **Get feedback on the README.** Share it with stakeholders, users, or teammates. Iterate on the documented interface before writing code.
3. **Write tests against the documented behavior.** The README is the specification -- tests verify the specification.
4. **Implement until tests pass.** The code serves the documented interface, not the other way around.
5. **Update the README if the implementation reveals design flaws.** Keep the README as the source of truth.

## What the README Should Contain

- **Title and one-paragraph description:** What is this and why does it exist?
- **Installation:** How to get it running.
- **Usage:** Realistic examples of the primary use cases. Show the happy path.
- **API / CLI overview:** The public interface at a glance. Detailed reference can live elsewhere.
- **Configuration:** Key options and their defaults.
- **Contributing:** How to set up for development.

## Constraints

- The README is deliberately limited to a single file. This prevents waterfall-style over-specification.
- Do not write exhaustive API reference in the README. The README covers the 80% use case. Reference docs are generated from code.
- Do not include implementation details. The README describes the external interface, not the internal architecture.

## Variants

**Documentation-Driven Development (DDD):** Extends RDD to the full documentation set -- not just the README, but tutorials, reference docs, and guides are written before code. The complete lifecycle: write docs, get feedback, TDD against docs, ship, publish docs, version.

**Amazon Working Backwards (PR-FAQ):** Before engineering begins, write a press release announcing the finished product and a FAQ addressing customer questions. Forces customer-centric thinking before technical decisions are made.

## When to Use

- Starting a new project, library, or CLI tool.
- Designing a new public API or module interface.
- Any work where the user-facing interface is the primary deliverable.

## When to Skip

- Internal implementation details that have no user-facing surface.
- Exploratory prototypes where the interface is intentionally fluid.
- Bug fixes and refactors that do not change external behavior (update the README only if it was wrong).
