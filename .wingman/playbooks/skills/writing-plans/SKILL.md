---
name: writing-plans
description: Use when design is complete and you need detailed implementation tasks - creates tracked MCP issues with exact file paths, complete code examples, and verification steps. Optional architecture plan published as a section on the spec issue.
---

{{#if spec_reference}}

## Workflow Context

Specification to plan: {{spec_reference}}
{{/if}}

# Writing Plans

## Overview

Write comprehensive implementation plans assuming the engineer has zero context for our codebase. Document everything they need to know: which files to touch for each task, code, testing, docs they might need to check, how to test it. Give them the whole plan as bite-sized tasks. DRY. YAGNI. TDD. Frequent commits.

**MCP issues are the source of truth** - each tracked task contains complete implementation details.

Optionally create:

- **Plan section** (appended to spec issue body) - Architecture overview and design decisions

**Works in main branch OR worktree (no preference)**

**Headless mode:** If run as subagent, do not stop to ask for feedback during execution.

## Bite-Sized Task Granularity

**Each step is one action (2-5 minutes):**

- "Write the failing test" - step
- "Run it to make sure it fails" - step
- "Implement the minimal code to make the test pass" - step
- "Run the tests and make sure they pass" - step
- "Commit" - step

## Plan Document (Reference Only)

Plan documents capture architecture/design context when the implementation involves multiple interconnected steps/issues. Plans are appended as a section at the bottom of the spec issue, keeping everything in one discoverable place.

### Create Plan When

Create a plan section when ANY of these are true:

- **10+ issues/tasks** - Need architectural overview to understand big picture
- **Multiple interconnected components** - Need system diagram and component relationships
- **Significant design decisions** - Need to document rationale and alternatives considered
- **Complex patterns/conventions** - Need reference material that applies across multiple tasks
- **Onboarding context needed** - Future agents/sessions would struggle understanding issues in isolation

Skip the plan when:

- **< 5 simple tasks** - Straightforward implementation, issues are sufficient
- **No architectural complexity** - Changes are localized and self-explanatory
- **Obvious approach** - No design decisions requiring rationale

### Plan Content

The plan should contain:

- Architecture overview and design decisions
- Technology choices and rationale
- Cross-cutting concerns and patterns
- Component relationships and system diagram
- Design alternatives considered and why rejected
- References to MCP issues (NOT duplicate content)

Use this template for the plan section content:

```markdown
# [Feature Name] Implementation Plan

**Goal:** [One sentence describing what this builds]

**Spec:** [link to GitHub issue]

**Architecture:** [2-3 sentences about approach]

**Tech Stack:** [Key technologies/libraries]

**Implementation tracking:** See GitHub issues (project: [specification reference])

---

## Acceptance Criteria Coverage

### Spec Acceptance Criteria

- AC-001: [description]
- AC-002: [description]
  ...

### Task-to-AC Mapping

| Task ID | Satisfies AC   | Estimated Compliance Contribution |
| ------- | -------------- | --------------------------------- |
| ISS-001 | AC-001, AC-003 | 15%                               |
| ISS-002 | AC-002         | 10%                               |

### Coverage Summary

- Total AC: 15
- AC covered by tasks: 14 (93%)
- AC with no implementing tasks: AC-012
- Estimated post-execution compliance: 93%

### Risk Areas

- [List any AC with no tasks or partial coverage]

---
```

### Publishing the Plan

Use `issues_update` to append the plan section to the spec issue body:

1. Fetch the current spec issue body via `issues_get` with `include_body: true`.
2. Concatenate the plan section (using the template above) to the end of the body.
3. Call `issues_update` with the complete updated body.

End the appended plan section with a summary line:

```
**Issues created:** [N] tasks in project [spec reference]
**Estimated compliance:** [X]% (based on task-to-AC mapping)
```

## MCP Issue Content Requirements

**Each issue MUST contain:**

- Exact file paths (create/modify/test)
- Complete code examples (not "add validation")
- Exact commands with expected output
- All 5 TDD steps (write test, verify fails, implementing-issue, verify passes, commit)
- Clear acceptance criteria
- Dependencies on other tasks

**NOT in issues:**

- Architecture rationale (goes in optional plan section on spec issue)
- Design alternatives considered (goes in optional plan section on spec issue)
- Cross-cutting concerns (goes in optional plan section on spec issue)

## Remember

- MCP issues are source of truth
- Include complete implementation details in each issue
- Optional plan section (on spec issue) for architecture/design context only
- No duplication between issues and plan section
- Reference relevant skills with @ syntax
- DRY, YAGNI, TDD, frequent commits

## Planning Process

### Phase 1: Read and Analyze Specification

1. Read the specification file completely
2. Review existing codebase to understand:
   - What's already implemented
   - Existing patterns to follow
   - Where new code should live
3. Think deeply about best implementation approach
4. Consider architecture, design patterns, maintainability

5. **Extract acceptance criteria from specification**

   Identify all acceptance criteria:
   - AC explicitly labeled (AC-001, AC-002, etc.)
   - Requirements from "Acceptance Criteria" sections
   - Test requirements from "Testing" sections
   - Manual verification steps
   - E2E test requirements

   Store as structured list for task mapping in Phase 2.

   **Note:** If spec has no explicit acceptance criteria, note this for later phases.

### Phase 2: Plan Task Breakdown

1. Break specification into logical tasks
2. For each task, prepare complete details:
   - Exact files to create/modify
   - Complete code examples for all 5 TDD steps
   - Test requirements
   - Exact commands and expected output
   - Commit message

**Think deeply** about task ordering:

- Tasks should build on each other incrementally
- Each task should be small (<250 LOC when implemented)
- No gaps - every step builds on earlier work

3. Review and refine task breakdown
4. Ensure tasks are right-sized and ordered correctly

5. **Map tasks to acceptance criteria**

   For each task, determine which acceptance criteria it satisfies:
   - Analyze task description and implementation scope
   - Identify which AC are fully/partially addressed
   - Calculate estimated compliance contribution per task
   - Flag any AC with no implementing tasks

   This mapping will be used to:
   - Generate coverage report in plan section (if created)
   - Enable REVIEW phase validation in implementing-specs workflow

6. Optionally draft plan section for architecture overview (if needed)

### Phase 3: Create MCP Issues (Source of Truth)

**Before creating issues, check for an existing plan:**

```typescript
// Check if issues already exist for this spec
const existing = issues_list({ parentTaskId: "{SPEC_ID}" });
```

If issues already exist linked to the spec, do NOT create duplicates. Instead:
- In headless/workflow mode: skip issue creation, report that plan already exists
- In interactive mode: show the existing issues and ask the user whether to replace them or skip

**MCP issues contain ALL implementation details.** For each task:

1. Call `issues_create` with complete information
2. **Immediately call `issues_set_parent`** to link it to the spec

```typescript
// Step 1: Create the issue
const issue = issues_create({
  title: "Task N: [Short action-oriented title]",
  description: `## Description
[What this task implements - refer to specification]

## Context
Reference: Spec #{SPEC_ID}
[Relevant architectural context and design decisions]

## Files
- Create: \`path/to/new/file.ts\`
- Modify: \`path/to/existing.ts:45-67\`
- Test: \`path/to/test.ts\`

## Implementation

**Step 1: Write the failing test**

\`\`\`typescript
// Complete test code
describe('feature', () => {
  it('should behave correctly', () => {
    // Exact test implementation
  });
});
\`\`\`

**Step 2: Run test to verify it fails**

Run: \`npm test -- path/to/test.ts\`
Expected: FAIL with "[expected error message]"

**Step 3: Write minimal implementation**

\`\`\`typescript
// Complete implementation code
export function feature() {
  // Exact minimal implementation
}
\`\`\`

**Step 4: Run test to verify it passes**

Run: \`npm test -- path/to/test.ts\`
Expected: PASS

**Step 5: Commit**

\`\`\`bash
git add path/to/test.ts path/to/file.ts
git commit -m "feat: add specific feature"
\`\`\`

## Acceptance Criteria
- [ ] Test written and failing (RED)
- [ ] Implementation passes test (GREEN)
- [ ] Code refactored if needed (REFACTOR)
- [ ] All tests passing
- [ ] Committed with clear message

## Dependencies
- Requires completion of: Task [N-1]
`,
  type: "issue",
  status: "open",
  priority: "medium", // or high/low based on criticality
  labels: ["plan-step", "implementation"],
  governanceContext: {
    agentId: "plan-executor",
    parentTaskId: "{SPEC_ID}",
  }
});

// Step 2: Link as sub-issue of the spec (REQUIRED)
issues_set_parent({ issueId: issue.id, parentId: "{SPEC_ID}" });
```

**For non-code tasks** (config files, CI YAML, documentation, infrastructure): adapt the template. Replace the TDD steps with appropriate verification steps (e.g., "validate YAML syntax", "verify action runs with dry-run", "check rendered output").

**Important**:

- Issues are the single source of truth - include complete code examples and exact commands
- Always set `parentTaskId` AND call `issues_set_parent` for every issue -- both are required
- Use `issues_update` if you need to refine an issue after creation (don't create duplicates)
- If creating optional plan section, it should reference issues, NOT duplicate their content

### Phase 4: Verify and Report

1. Use `issues_list` filtered by `parentTaskId: "{SPEC_ID}"` to verify all issues created and linked
2. Review issue list to ensure:
   - All tasks from plan are present
   - Issues are in correct order
   - No duplicates or gaps
   - Task sizes are appropriate
3. Make any necessary adjustments using `issues_update`

## Execution Handoff

After creating issues, offer execution choice:

**"Plan complete:**

- **Issues created**: [N] tasks in issue tracker (project: [spec])
- **Plan section** (if created): appended to spec issue (includes AC coverage analysis)
- **Estimated compliance**: [X]% (based on task-to-AC mapping)

## Issue Update Pattern

If you discover during planning that a task needs refinement:

```typescript
// Instead of creating new issue
issues_update({
  id: "[issueId]",
  priority: "high", // change priority
  description: "[updated description]", // add more detail
  labels: ["plan-step", "implementation", "complex"], // add label
});
```

## Checklist Before Completion

- [ ] Specification fully read and analyzed
- [ ] Existing codebase reviewed for patterns
- [ ] Task breakdown created and refined
- [ ] Every task has corresponding MCP issue with COMPLETE details
- [ ] All issues include exact file paths, code examples, commands
- [ ] All issues linked to spec via `issues_set_parent`
- [ ] Issue list reviewed and verified (using `issues_list(parentTaskId: spec_id)`)
- [ ] Tasks are ordered correctly
- [ ] No gaps in implementation steps
- [ ] Optional plan section appended to spec issue (if architecture context needed)
- [ ] Plan covers entire specification without overscoping

## Integration with Specifications

When working from a specification created with `writing-specifications` skill:

- Reference the spec issue ID in all issues (via `parentTaskId` and `issues_set_parent`)
- Follow any implementation notes in spec
- Honor constraints documented in spec
- Use architecture decisions from spec
- Create issues that map to spec's acceptance criteria
