---
name: verify-integration
description: Traces every spec sub-requirement through to integrated implementation, fixes integration gaps, and gates on compliance. Use between implement and review-pr phases.
---

{{#if spec_reference}}
## Workflow Context
Specification: {{spec_reference}}
{{/if}}

<purpose>
Verify that every acceptance criterion sub-requirement from the spec has been implemented, integrated into consumers, and is substantive -- not a hollow stub. Dispatch targeted fix agents for integration gaps. Gate on compliance before proceeding to PR review.
</purpose>

<required_context>

## Inputs

| Variable | Required | Description |
|----------|----------|-------------|
| `{SPEC_REFERENCE}` | Yes | Spec issue number or identifier |
| `{in_workflow_worktree}` | No | Worktree path if running in a workflow |

### Worktree Context

When `{in_workflow_worktree}` is provided, all operations -- file searches, test runs, git commands, and subagent dispatches -- MUST target that worktree path rather than the current working directory. Prefix all bash commands with `cd {in_workflow_worktree} &&` and pass the worktree path to subagents as their working directory.

## MCP Tool Requirements

| Tool | Purpose |
|------|---------|
| `issues_get` | Read spec and plan task details |
| `issues_list` | Find plan tasks linked to spec |
| `issues_create` | Create targeted fix issues |

</required_context>

<process>

<step name="extract" priority="first">

## Phase 1: Extract Sub-Requirements

1. Read the spec via `issues_get(id: "{SPEC_REFERENCE}", include_body: true)`
2. Extract all acceptance criteria and decompose into atomic sub-requirements (same decomposition as writing-plans)
3. Load the plan's task-to-sub-requirement mapping via `issues_list(parentTaskId: "{SPEC_REFERENCE}")`
4. Build a traceability matrix: sub-requirement -> plan task -> expected artifact

</step>

<step name="trace">

## Phase 2: Trace Implementation

For each sub-requirement in the traceability matrix:

### Individual Task Verification

1. **Locate artifact**: Find the implementing code from the plan task
2. **Check integration**: Search for imports of the artifact outside its own file and test files
3. **Evaluate substance**: Use your judgment to determine whether the implementation actually performs the non-trivial work its sub-requirement demands. Look for implementations that are hollow -- functions that exist but do not do real work. Do NOT rely on pattern-matching specific code tokens; instead, read the implementation and assess whether it fulfills the requirement's intent.
4. **Check for deviations**: Look for `DEVIATION:` markers in recent commits (`git log --grep="DEVIATION:" --oneline`) and in code comments. These indicate intentional simplifications documented by the implementing agent.

### Cross-Task Integration Verification

5. **Verify wiring between tasks**: For sub-requirements that span multiple tasks or depend on other tasks' outputs, verify the integration points are connected -- exported symbols are imported, event handlers are registered, configuration is threaded through, etc.
6. **End-to-end path check**: For each acceptance criterion (which may encompass multiple sub-requirements), trace the full path from entry point to outcome. Confirm that the components produced by different tasks actually compose into a working feature, not just isolated pieces.

### Classification

Classify each sub-requirement as:
- **INTEGRATED**: Artifact exists, imported by consumers, contains substantive implementation that fulfills the requirement
- **BUILT_NOT_WIRED**: Artifact exists with real implementation but has zero consumer imports outside tests
- **STUBBED**: Artifact exists and is imported but implementation is hollow or incomplete -- does not perform the real work the requirement demands
- **MISSING**: No implementing artifact found
- **DEVIATED**: The sub-requirement was intentionally not implemented as specified, with documented rationale (a `DEVIATION:` marker exists in commits or code comments explaining why). Record the rationale.

</step>

<step name="remediate">

## Phase 3: Remediate Gaps (max 2 iterations)

Execute a remediation loop with a maximum of **2 iterations**. Each iteration addresses all actionable gaps found in the current state.

### Per-Iteration Actions

For each BUILT_NOT_WIRED sub-requirement:
- Create a targeted fix issue describing the wiring needed
- Dispatch a subagent to implement the consumer integration
- Re-verify after fix

For each STUBBED sub-requirement:
- Create a targeted fix issue describing the real implementation needed
- Dispatch a subagent to replace the stub with real logic
- Re-verify after fix

For each MISSING sub-requirement:
- Classify as FUNDAMENTAL_GAP (cannot auto-fix)
- Include in escalation report

DEVIATED sub-requirements are NOT gaps -- they are tracked for human review but require no remediation.

### Iteration Bound

After each iteration, re-run the trace from Phase 2 to update classifications. If all actionable gaps are resolved, exit the loop early. If gaps persist after **2 iterations**, stop remediation and proceed to Phase 4 with the remaining gaps included in the compliance report.

</step>

<step name="verify">

## Phase 4: Final Verification

1. Run full test suite to confirm fixes do not break existing functionality
2. Calculate compliance score:
   - Numerator: count of INTEGRATED + DEVIATED sub-requirements
   - Denominator: total sub-requirements
   - `COMPLIANCE = (numerator / denominator) * 100`

DEVIATED items count toward compliance (they are accounted for, with documented rationale) but are surfaced prominently for human review.

<gate type="yes-no">

### Compliance Gate

Every sub-requirement must be accounted for as either INTEGRATED or DEVIATED. There is no reason for a sub-requirement to be unclassified.

| Score | Action |
|-------|--------|
| 100% | PASS -- proceed to review-pr |
| < 100% | FAIL -- escalate with gap report |

**Escalation format (< 100%):**
```
Verify-Integration: Compliance Below Threshold
Score: X% (100% required)
Gaps: [list of BUILT_NOT_WIRED, STUBBED, and MISSING sub-requirements]
Remediation attempts: 2 (max reached) / N gaps resolved, M remaining
Fix attempts: [summary of remediation actions taken]
```

### Deviations Report

Regardless of pass/fail, include a prominent deviations section in the final output:

```
## Deviations from Spec (requires human review)

| Sub-Requirement | Rationale | Source |
|-----------------|-----------|--------|
| SR-003: batch processing | Implemented sequential processing instead; batch adds complexity without benefit at current scale | commit abc1234 |
| SR-007: retry with backoff | Used simple retry (max 3) instead of exponential backoff; sufficient for MVP | code comment in retry.ts:42 |
```

If no deviations exist, state: "No deviations from spec."

</gate>

</step>

</process>

<success_criteria>
- [ ] All spec sub-requirements extracted and traced to implementing artifacts
- [ ] Cross-task integration points verified (components wired together, not just built in isolation)
- [ ] Acceptance-criteria-level compliance verified (full path from entry point to outcome)
- [ ] Each sub-requirement classified (INTEGRATED / BUILT_NOT_WIRED / STUBBED / MISSING / DEVIATED)
- [ ] BUILT_NOT_WIRED and STUBBED gaps remediated where possible (max 2 iterations)
- [ ] Full test suite passes after remediation
- [ ] Compliance score calculated and gate applied (100% required)
- [ ] All DEVIATED items surfaced with rationale for human review
</success_criteria>

<error_handling>

| Scenario | Recovery |
|----------|----------|
| Spec not found | STOP with error |
| No plan tasks linked | STOP -- plan must exist before verification |
| Fix subagent fails | Record as unresolved gap, continue with other fixes |
| Test suite fails after fix | Revert fix, record gap, continue |
| Gaps persist after 2 remediation iterations | Stop remediation, include remaining gaps in escalation report |

</error_handling>
