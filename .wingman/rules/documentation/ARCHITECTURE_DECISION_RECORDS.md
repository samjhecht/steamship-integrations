---
name: Architecture Decision Records
description: Record significant technical decisions with context, rationale, and consequences using ADRs
category: documentation
paths:
  - "**/decisions/**"
  - "**/adr/**"
  - "**/ADR-*"
---

# Architecture Decision Records (ADR)

Record significant architectural and technical decisions in lightweight, structured documents. ADRs capture the "why" behind decisions so future developers understand the context that no longer exists in anyone's head.

## When to Write an ADR

- Choosing between two or more technical approaches.
- The "why" behind a decision would be non-obvious to future developers.
- A decision affects multiple teams or components.
- Adopting, replacing, or removing a dependency or technology.
- Changing a convention or standard that affects how code is written across the project.

## When to Skip

- Trivial, low-risk, self-contained decisions.
- Decisions already covered by existing standards.
- Temporary workarounds or experiments.
- Choices with only one viable option (nothing to decide).

## ADR Format

```markdown
# ADR-NNN: Title (short noun phrase)

## Status

Proposed | Accepted | Deprecated | Superseded by ADR-XXX

## Context

Describe the forces at play -- technical, business, social.
Use value-neutral language. State facts.

## Decision

State the decision in active voice, full sentences.

## Consequences

Describe resulting context -- both positive and negative.
```

## Rules

- Keep ADRs to 1-2 pages.
- Write as if talking to a future developer who has no context on the decision.
- Link to supplemental material rather than embedding it.
- ADRs are immutable once accepted. To reverse a decision, write a new ADR that supersedes the old one and update the old ADR's status.
- Store ADRs in a predictable location (`docs/decisions/`, `adr/`, or similar) with sequential numbering.
- Include the date the decision was made.

## Lifecycle

| Status | Meaning |
|--------|---------|
| **Proposed** | Under discussion, not yet agreed upon |
| **Accepted** | Agreed and in effect |
| **Deprecated** | No longer relevant (e.g., feature removed) |
| **Superseded** | Replaced by a newer ADR (link to it) |

## Anti-patterns

- Writing ADRs after the fact with reconstructed rationale -- capture decisions when they are made, while context is fresh.
- ADRs that describe "what" without explaining "why" -- the code already shows what was done.
- Treating ADRs as approval gates that block progress -- they document decisions, not control them.
- Letting ADR status go stale -- if a decision has been reversed, update the original.
