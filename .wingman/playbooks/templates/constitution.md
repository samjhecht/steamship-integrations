---
wingmanVersion: "1.2.0"
ratified: "{RATIFIED_DATE}"
lastAmended: "{LAST_AMENDED_DATE}"
---

# {PROJECT_NAME} Constitution

## Vision

{A concise articulation of the project's core purpose, functionality, and value. This is the north star that all design decisions align against.}

**Example**: "Build a lightweight, maintainable task management system that prioritizes user privacy and offline-first operation."

## Governance Rules

1. **Constitution is supreme law**: This document overrides all other practices and decisions
2. **All features must align**: Every PR, issue, and spec must demonstrate constitutional alignment
3. **No exceptions**: Principles are non-negotiable unless formally amended via the `amending-constitution` skill
4. **Regular review**: Revisit quarterly to ensure principles still serve the mission

## Core Design Principles

### 1. {First Principle Name}

**Principle**: {One-sentence statement of the principle}

**In Practice**:

- {Specific application 1}
- {Specific application 2}
- {Specific application 3}

**Anti-patterns** (what NOT to do):

- {Anti-pattern 1 with explanation}
- {Anti-pattern 2 with explanation}

**Examples**:

- Good: {Concrete example showing principle in action}
- Bad: {Concrete example violating principle}

---

## Open Questions

High-level project decisions not yet fully resolved. These are tracked as `open-question` type issues so they surface during roadmap reviews and agent meta-analysis.

Examples of open questions:
- Unresolved architectural direction (e.g. "When should we prioritize merging project X and Y?")
- Ambiguities in vision or principles that haven't caused problems yet but may
- Contradictions between constitutional principles and detailed standards
- Strategic questions raised by agents during implementation or review

To file an open question: `issues_create({ type: "open-question", ... })`

To review open questions: `issues_list({ type: "open-question", status: ["open"] })`

## References

Progressive disclosure -- detailed standards live in dedicated files:

| Document | Location | Purpose |
|----------|----------|---------|
| Design Principles | `.wingman/rules/DESIGN.md` | Detailed design philosophy and architectural guidelines |
| Coding Standards | `.wingman/rules/CODING.md` | Language-specific coding conventions and patterns |
| Testing Standards | `.wingman/rules/TESTING.md` | Test strategy, coverage goals, and testing patterns |
| Security Standards | `.wingman/rules/SECURITY.md` | Security policies, data handling, and threat model |
| Version History | `.wingman/memory/constitution-versions/` | Snapshots of each major constitutional version |

## Amending This Constitution

To propose changes to constitutional principles, use the `amending-constitution` skill. This creates a `constitutional-amendment` issue with structured fields for rationale, impact analysis, and migration plan.

**Version scheme**:
- Major (1.0.0 -> 2.0.0): New principle added or principle removed
- Minor (1.0.0 -> 1.1.0): Existing principle modified
- Patch (1.0.0 -> 1.0.1): Clarification or example added

## Version History

- **1.0.0** ({RATIFIED_DATE}): Initial constitution ratified
