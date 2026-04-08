---
description: Reviews specs, plans, or GitHub issues for completeness, feasibility, task scoping, and missing considerations. Use when you have a plan, specification, or issue and want to validate it before implementation begins.
---

{{#if spec_reference}}
## Workflow Context
Specification: {{spec_reference}}
{{/if}}
{{#if issue_ids}}
Issue IDs to review: {{issue_ids}}
{{/if}}

<purpose>
Review a plan, specification, or GitHub issue for completeness, feasibility, task scoping, and missing considerations. Dispatches a single reviewer subagent with codebase access to verify that referenced files, packages, and APIs actually exist.
</purpose>

<required_context>

## Arguments

- **Plan/spec identifier** (optional): one of the following:
  - A wingman issue or spec ID (e.g., `42` for GitHub, or `SPEC-000042` / `ISS-000015` for markdown)
  - A GitHub issue number (e.g., `#42`) or full URL
  - A file path to a markdown document
  - Empty -- search the current conversation context for a plan or spec

## MCP Tools

| Tool | Purpose |
|------|---------|
| `issues_get` | Fetch plan/spec content by ID (with `include_body: true`) |
| `issues_list` | List related issues for context |
| `issues_create` | Create follow-up issues from findings |

</required_context>

<available_agent_types>

| Agent | Purpose |
|-------|---------|
| plan-reviewer | Reviews a plan, spec, or issue for completeness, feasibility, task scoping, and missing considerations. Requires codebase access (Read, Glob, Grep, Bash). |

</available_agent_types>

<process>

<step name="resolve-input" priority="first">

## Phase 1: Resolve Input

Determine what was provided and fetch the full text content of the plan.

### Step 1: Classify the identifier

Check the identifier against these patterns, in order:

1. **Wingman ID** -- matches `SPEC-NNNNNN` or `ISS-NNNNNN`
2. **GitHub issue number** -- matches `#N` or a bare integer
3. **GitHub URL** -- contains `github.com` and `/issues/`
4. **File path** -- a string containing `/` or ending in `.md`
5. **Empty** -- no identifier provided

### Step 2: Fetch content

- **Wingman ID**: Use the `issues_get` MCP tool with the ID and `include_body: true` to fetch the full content.
- **GitHub issue number or URL**: Run `gh issue view {NUMBER_OR_URL}` via Bash to fetch the issue body.
- **File path**: Read the file directly.
- **Empty**: Look through the current conversation for a plan, specification, or issue body. If none is found, ask the user to provide one and stop.

### Step 3: Validate

Confirm you have substantive content to review. If the fetched content is empty or trivially short (fewer than 3 sentences), inform the user and stop.

Store the resolved content as `{PLAN_CONTENT}` and the identifier (or a short descriptive label) as `{PLAN_IDENTIFIER}`.

</step>

<step name="dispatch-reviewer">

## Phase 2: Dispatch Reviewer

Assemble the reviewer prompt and dispatch a single subagent to perform the review.

### Step 1: Load prompt components

Read the following files. If a file does not exist, skip it gracefully and note its absence.

1. **Base reviewer preamble**: `.wingman/playbooks/prompts/system/reviewer.md`
2. **Plan review prompt**: `.wingman/playbooks/prompts/reviews/plan-review.md`

### Step 2: Assemble the subagent prompt

Build the prompt by concatenating sections in this order:

```
[Base reviewer preamble -- full content of reviewer.md]

[Plan review prompt -- body only, strip any YAML frontmatter]

## Plan to Review

Identifier: {PLAN_IDENTIFIER}

{PLAN_CONTENT}
```

If the plan review prompt file (`.wingman/playbooks/prompts/reviews/plan-review.md`) was not found, use this inline fallback as the plan review section:

```
You are reviewing a plan, specification, or issue for implementation readiness. Evaluate:

1. **Completeness**: Are all requirements clearly stated? Are acceptance criteria defined? Are edge cases addressed?
2. **Feasibility**: Can this be implemented with the current codebase and dependencies? Do referenced files, modules, and APIs exist?
3. **Task scoping**: Are tasks appropriately sized? Are dependencies between tasks identified? Is the ordering logical?
4. **Missing considerations**: Are there error handling gaps? Security implications not addressed? Performance concerns? Testing strategy omissions?
5. **Clarity**: Are requirements unambiguous? Could two developers interpret them differently?

For each finding, provide:
- Severity: critical, warning, or nit
- Category: Completeness, Feasibility, Task Scoping, Clarity, Security, Testing, Performance, or Missing Consideration
- A specific description of the issue
- A concrete suggestion for how to address it

When the plan references specific files, packages, or APIs, verify they exist in the codebase using the tools available to you. If something references an external system or service you cannot verify, flag it as "unverifiable" rather than failing it.

Return your findings as structured text, not JSON.
```

### Step 3: Dispatch the subagent

Use the Agent tool to dispatch a single reviewer subagent with the assembled prompt. Use `subagent_type: "plan-reviewer"` so that the correct rule profile is injected automatically. The subagent needs codebase access to verify feasibility, so it requires these tools: Read, Glob, Grep, Bash.

Wait for the subagent to complete and capture its full response as `{REVIEWER_OUTPUT}`.

</step>

<step name="present-findings">

## Phase 3: Present Findings

Format the reviewer output into a structured report and present it to the user.

### Step 1: Parse and format

Transform the reviewer output into this format:

```markdown
## Plan Review: {PLAN_IDENTIFIER}

**Verdict:** PASS / NEEDS REVISION
**Summary:** One-line assessment

### Findings

| # | Severity | Category | Finding | Suggestion |
|---|----------|----------|---------|------------|
| 1 | warning | Feasibility | ... | ... |
| 2 | critical | Completeness | ... | ... |

### Missing Considerations
- [List any gaps the reviewer identified that do not fit neatly into the table]

### Feasibility Assessment
- **Verified**: [List files, packages, or APIs that were confirmed to exist]
- **Unverifiable**: [List external systems or services that could not be checked]
```

### Verdict Rules

- **PASS**: Zero critical findings and two or fewer warnings
- **NEEDS REVISION**: Any critical findings, or three or more warnings

If the reviewer found no issues at all, the Findings table, Missing Considerations, and Feasibility Assessment sections can be replaced with a single line: "No issues found."

### Step 2: Handle Verdict

#### Auto-Remediation

When the verdict is NEEDS REVISION:

Auto-remediation targets **coverage gaps** (uncovered sub-requirements). Other critical findings (feasibility, architectural concerns) cannot be auto-remediated and should be included in the escalation report if they persist.

**Remediation (max 1 iteration):**

1. Read the gap list from the review findings (critical findings about uncovered sub-requirements)
2. For each uncovered sub-requirement:
   - Create a new task issue via `issues_create` with:
     - Title: "Task N+1: Cover [sub-requirement description]"
     - Description: Standard issue template with file paths, implementation details
     - Labels: `["plan-step", "implementation", "auto-remediation"]`
     - `governanceContext.parentTaskId`: the spec ID
   - Call `issues_set_parent` to link it to the spec
3. **Verify task coverage** (do NOT re-dispatch the full reviewer): For each sub-requirement from the gap list, confirm that a task issue now exists that covers it. Use `issues_list` with the spec as parent to enumerate existing tasks and match them against the gap list. This is a simple existence check, not a full re-review.
4. If all gaps have corresponding tasks: update verdict to PASS and proceed
5. If any gap lacks a corresponding task (e.g., `issues_create` failed): escalate with a detailed gap report

**Escalation format:**
```
Auto-Remediation Incomplete
Remaining gaps without tasks: [list of uncovered sub-requirements]
Tasks created: [list of auto-remediated task IDs]
Action required: Manual creation of missing tasks
```

</step>

</process>

<success_criteria>
- [ ] Plan/spec identifier resolved and content fetched successfully
- [ ] Reviewer subagent dispatched with correct prompt (preamble + review prompt + plan content)
- [ ] Reviewer output parsed into structured findings table
- [ ] Verdict assigned correctly based on severity counts (PASS vs NEEDS REVISION)
- [ ] Feasibility assessment includes verified and unverifiable items
- [ ] Auto-remediation creates gap-filling tasks when verdict is NEEDS REVISION
- [ ] Task coverage verified via existence check (not full re-review)
</success_criteria>
