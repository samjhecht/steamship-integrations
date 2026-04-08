---
name: docs-audit-review
description: Reviews documentation fix PRs for completeness and accuracy against their source issues. Acts as a quality gate before merge.
---

<purpose>
Review documentation fix PRs created by docs-audit-fix, verifying each PR's changes against its linked issue's acceptance criteria and current code reality. Approve passing PRs and flag failing ones with specific findings. This is the third phase of the docs-audit workflow.
</purpose>

<required_context>

## Prerequisites

- `pr_numbers` input from docs-audit-fix (array of PR numbers)
- `gh` CLI installed and authenticated
- MCP tools available: `issues_get` (with `include_body: true`)
- Access to project source tree for accuracy verification

</required_context>

<available_agent_types>

This is a **workflow skill** -- it coordinates parallel reviewer subagents, one per PR.

- **Reviewer Agent (N instances):** Each reviews one PR against its linked issue's acceptance criteria

</available_agent_types>

<process>

<step name="setup" priority="first">

### Phase 1: Setup (Sequential)

**Why sequential:** Need to map PRs to their source issues before dispatching reviewers.

**Task:** Build a review manifest mapping each PR to its acceptance criteria.

**Approach:**

1. Read the `pr_numbers` input to get the list of PR numbers
2. For each PR number:
   - Fetch PR metadata: `gh pr view <number> --json body,title,number`
   - Extract linked issue numbers from PR body (parse `Closes #X` / `Fixes #X` references)
   - For each linked issue, fetch via `issues_get` MCP tool (with `include_body: true`)
   - Extract the acceptance criteria checklist from the issue description
3. Build a review manifest:
   ```
   PR #101:
     Issues: [#45, #46]
     Acceptance criteria:
       - [ ] Fix broken path to gateway/README.md
       - [ ] Update MCP tool parameter docs
       - [ ] Add missing code example for workflow_run
   ```

**Output:** Review manifest mapping PR numbers to acceptance criteria.

</step>

---

<step name="parallel-review">

### Phase 2: Parallel Review

**Why parallel:** Each PR review is independent -- dispatch all reviewers in a single message.

Launch **one reviewer subagent per PR** using the Task tool. All Task calls in a **single message** for true parallelism.

Each reviewer agent receives these instructions:

**Reviewer Agent Instructions:**

1. **Read the PR diff:**
   ```bash
   gh pr diff <number>
   ```

2. **Check every acceptance criterion:**
   For each criterion from the linked issue:
   - Verify the PR diff addresses it
   - Mark as MET or NOT_MET with specific evidence

3. **Check for regressions:**
   - Verify no existing correct content was removed
   - Verify no new broken cross-references were introduced
   - Verify no documentation was accidentally deleted
   - Check that markdown formatting is valid (tables, code blocks, headers)

4. **Verify accuracy against current code:**
   - For any file paths mentioned in the changes, verify they exist
   - For any API parameters documented, spot-check against actual code
   - For any code examples added or modified, verify they are syntactically valid

5. **Produce verdict:**

   If all acceptance criteria are met and no regressions found:
   ```
   DOCS_REVIEW_PASS

   PR #<number>: All [N] acceptance criteria met.
   No regressions detected.
   ```

   If any criteria are not met or regressions found:
   ```
   DOCS_REVIEW_FAIL

   PR #<number>: [M]/[N] acceptance criteria met.

   NOT MET:
   - <criterion> -- <what is missing or wrong>

   REGRESSIONS:
   - <description of regression>
   ```

</step>

---

<step name="consolidation">

### Phase 3: Consolidation (Sequential)

**Why sequential:** Needs results from all reviewer agents to take action.

**Task:** Process review verdicts and take appropriate action on each PR.

**Approach:**

**3.1 Process Verdicts**

For each reviewer result:

- Parse the `DOCS_REVIEW_PASS` or `DOCS_REVIEW_FAIL` status line
- Collect specific findings

**3.2 Act on Results**

For `DOCS_REVIEW_PASS`:
- Approve the PR:
  ```bash
  gh pr review <number> --approve --body "Documentation changes verified against acceptance criteria. All criteria met, no regressions detected."
  ```

For `DOCS_REVIEW_FAIL`:
- Post review comments on the PR:
  ```bash
  gh pr review <number> --request-changes --body "Documentation review found issues:

  ## Unmet Acceptance Criteria
  - <criterion> -- <what is missing>

  ## Regressions
  - <regression description>

  Please address the above before merging."
  ```

**3.3 Generate Summary Report**

```
DOCS_AUDIT_REVIEW_COMPLETE

PRs reviewed: [count]
  Approved: [count]
  Changes requested: [count]

Approved PRs:
  #<pr_number> - <title>

PRs needing changes:
  #<pr_number> - <title>
    - <finding 1>
    - <finding 2>
```

**Output:** The review result object containing:
- `approved`: array of approved PR numbers
- `changesRequested`: array of `{ prNumber, findings }` for PRs that need changes
- `summary`: the summary report text

</step>

</process>

<success_criteria>
- [ ] Review manifest built mapping all PRs to their acceptance criteria
- [ ] Reviewer agents dispatched in parallel (single message with all Task calls)
- [ ] Each reviewer checked all acceptance criteria against the PR diff
- [ ] Each reviewer checked for regressions (removed content, broken links)
- [ ] Each reviewer verified accuracy against current code
- [ ] Passing PRs approved via `gh pr review --approve`
- [ ] Failing PRs given specific change requests via `gh pr review --request-changes`
- [ ] Consolidation report produced with pass/fail counts and findings
</success_criteria>

<error_handling>

## Failure Modes

**Reviewer agent fails to complete:**
- Note the PR as unreviewed in the summary
- Do not approve or reject -- leave in pending state
- Flag for manual review

**PR has been merged or closed before review:**
- Skip the PR
- Note as "already merged/closed" in the summary

**Linked issue not found:**
- Review the PR diff on its own merits without acceptance criteria
- Check for obvious regressions and accuracy issues
- Note "no acceptance criteria available -- reviewed for general quality only"

**Cannot determine pass/fail:**
- Default to `DOCS_REVIEW_FAIL` with specific concerns listed
- Request human review in the PR comment

</error_handling>
