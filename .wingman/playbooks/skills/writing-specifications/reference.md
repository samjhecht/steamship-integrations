# Specifications

**Purpose**: Define feature requirements, design decisions, and architectural patterns for this project.

**Status**: [X specifications active, Y specifications completed, Z specifications archived]

---

## Quick Reference

### Creating Specifications

**Via MCP Tool**:
```typescript
issues_create({
  title: "Specification: Feature Name",
  description: "Comprehensive feature description...",
  type: "specification",
  status: "open",
  priority: "high",
  labels: ["specification"],
  project: "Project Name"
})
```

**Via Skill**: Use the `writing-plans` skill or create manually

**Manual**: Create file `NNNNNN-feature-name.md` with YAML frontmatter (see template below)

### Specification Lifecycle

```
open (design) → in_progress (implementing) → closed (completed)
                      ↓
                cancelled (if abandoned)
```

---

## Specification Template

```markdown
---
id: "NNNNNN"
title: "Specification: Feature Name"
type: "specification"
status: "open"
priority: "high"
labels: ["specification", "phase-N"]
assignee: ""
project: ""
createdAt: "YYYY-MM-DDTHH:mm:ss.sssZ"
updatedAt: "YYYY-MM-DDTHH:mm:ss.sssZ"
governanceContext:
  agentId: ""
  constitutionalAlignment: "Aligns with Principle 1 (X), Principle 3 (Y)"
  roadmapPhase: "Phase N"
  estimatedEffort: ""
---

## Overview

[2-3 sentence description of what this feature does and why it matters]

## Constitutional Alignment

**Principles**: [Which principles from CONSTITUTION.md does this support?]

**Decision Framework**:
- ✅ Constitutional Alignment: [Explanation]
- ✅ User Value: [Explanation]
- ✅ Simplicity: [Explanation]
- ✅ Maintainability: [Explanation]
- ✅ Scope: [Explanation]

## Requirements

### Functional Requirements

1. **[Requirement Category]**
   - [Specific requirement 1]
   - [Specific requirement 2]

### Non-Functional Requirements

- **Performance**: [Constraints]
- **Security**: [Considerations]
- **Scalability**: [Considerations]

## Design Decisions

### [Decision 1 Title]

**Decision**: [What we're doing]
**Rationale**: [Why we're doing it this way]
**Alternatives Considered**: [What we rejected and why]
**Trade-offs**: [What we're giving up]

## Implementation Notes

[Technical details, constraints, dependencies]

## Testing Strategy

[How this will be tested]

## Acceptance Criteria

- [ ] [Criterion 1]
- [ ] [Criterion 2]

## References

- Related specifications: [Links]
- External documentation: [Links]
```

---

## Governance Files

### CONSTITUTION.md

**Purpose**: Supreme law - immutable design principles guiding all decisions

**Location**: `.wingman/CONSTITUTION.md`

**When to update**: Only via formal amendment process (see constitution itself)

**Key sections**:
- North Star mission
- Core design principles (5+)
- Decision framework
- Amendment process

### ROADMAP.md

**Purpose**: Strategic multi-phase plan showing project direction and tactical execution tracking

**Location**: `.wingman/ROADMAP.md`

**When to update**:
- Quarterly reviews
- Phase completions
- Major feature additions
- Constitutional amendments affecting roadmap
- After completing features (update status)
- Monthly reviews to update % complete

**Key sections**:
- Current state (completed features)
- Future phases with timelines and goals
- Technical debt tracking
- Links to constitution
- Tactical execution tracking with granular % completion

---

## Workflows

### Creating New Feature Specification

1. **Constitutional Check**: Review `.wingman/CONSTITUTION.md` for alignment
2. **Roadmap Check**: Determine which phase in `.wingman/ROADMAP.md`
3. **Create Specification**: Use template above
4. **Add Constitutional Alignment Section**: Explicitly state which principles
5. **Create Implementation Plan**: Use `writing-plans` skill
6. **Update ROADMAP**: Add to appropriate phase if not already there

### Implementing Specification

1. **Read Specification Thoroughly**: Understand all requirements
2. **Create Implementation Plan**: Use `writing-plans` skill (creates issues)
3. **Update Status**: Set specification to `in_progress`
4. **Follow TDD**: Use `test-driven-development` skill
5. **Track Progress**: Update `.wingman/ROADMAP.md` as features complete
6. **Mark Complete**: When all acceptance criteria met

### Amending Specification

1. **Document Reason**: Why is amendment needed?
2. **Check Constitutional Impact**: Does this affect alignment?
3. **Update Specification**: Make changes
4. **Update Related Issues**: Adjust implementation tasks
5. **Update NEXT_STEPS**: Reflect any scope changes
6. **Increment Version**: Add changelog entry at bottom

---

## Constitutional Governance

### Before Creating Any Specification

**MANDATORY CHECKS**:

1. **Constitutional Alignment**: Does this align with ALL principles in `.wingman/CONSTITUTION.md`?
2. **User Value**: Does this solve a real user problem?
3. **Simplicity**: Is this the simplest solution that works?
4. **Maintainability**: Can we maintain this long-term?
5. **Scope**: Does this fit our mission, or is it scope creep?

**If ANY answer is "no" or "unclear", reconsider or revise.**

### Documenting Constitutional Alignment

Every specification MUST include:

```markdown
## Constitutional Alignment

**Principles**: [List specific principles from CONSTITUTION.md]

**Alignment Explanation**:
- **Principle 1 ([Name])**: [How this spec aligns]
- **Principle 2 ([Name])**: [How this spec aligns]

**Potential Conflicts**: [Note any tension with other principles]
**Resolution**: [How conflicts are resolved]
```

### Using Constitution for Scope Control

**Constitution as firewall against scope creep**:

1. Feature request comes in
2. Check against constitutional principles
3. If doesn't align → Document why and reject/defer
4. If aligns → Proceed with specification

**Document rejections** in `.wingman/ROADMAP.md` changelog or in specification as "Alternatives Considered" to avoid revisiting.

---

## Roadmap Integration

### Phased Approach

All specifications should map to a roadmap phase:

**Current Phase** (from `.wingman/ROADMAP.md`):
- [Phase Name]: [Timeline]
- Focus: [Goal]
- Specifications: [List]

**Future Phases**:
- Track in `.wingman/ROADMAP.md`
- Specifications marked as `open` but not actively worked

### Progress Tracking

Use `.wingman/ROADMAP.md` for granular tracking:

**✅ Fully Implemented**: Specification closed, all acceptance criteria met

**⚠️ Partially Implemented**: Specification in progress, some features done

**❌ Not Implemented**: Specification open, no implementation yet

**Update frequency**: At minimum monthly, ideally after each feature completion

---

## Metrics (Auto-Updated)

**Total Specifications**: [N]

**By Status**:
- Open (Design): [X] ([Y]%)
- In Progress: [X] ([Y]%)
- Closed (Complete): [X] ([Y]%)
- Cancelled: [X] ([Y]%)

**By Roadmap Phase**:
- Phase 1: [X] specs
- Phase 2: [X] specs
- Phase 3: [X] specs

**Constitutional Compliance**:
- All specifications reviewed: [X]/[N] ([Y]%)
- Principles coverage: [Which principles are most referenced]

---

## Best Practices

1. **Specification before implementation** - Always write spec first
2. **Constitutional alignment mandatory** - Explicitly document
3. **Keep specifications current** - Update as design evolves
4. **Link to roadmap** - Show phase and strategic context
5. **Acceptance criteria = done** - Define success upfront
6. **Review quarterly** - Ensure specs still align with constitution
7. **Archive completed specs** - Keep directory focused on active work

---

**Related Documents**:
- `.wingman/CONSTITUTION.md` - Supreme law (read this first!)
- `.wingman/ROADMAP.md` - Strategic plan and tactical execution
- `../issues/README.md` - Implementation tracking
- `../../docs/mcp-usage.md` - MCP tools reference

**Last Updated**: [YYYY-MM-DD]
