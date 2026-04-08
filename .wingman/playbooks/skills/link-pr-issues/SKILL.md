---
description: Deterministically reconcile a PR's links to its governing spec and implementation issues. Use at the end of workflows after PR body rewrites or review automation.
---

{{#if spec_reference}}
## Workflow Context
Specification: {{spec_reference}}
{{/if}}
{{#if pr_number}}
PR to link: {{pr_number}}
{{/if}}

# Link PR Issues

## Goal

Make PR linkage deterministic and idempotent after all PR summary rewriting is complete.

This skill is the final metadata reconciliation pass. It should:

- resolve the canonical governing spec issue
- resolve child implementation issues for that spec
- ensure the PR body contains a machine-managed Related block
- call `issues_link_pr` for any missing links

## Machine-Managed Related Block

Use this exact block in the PR body:

```markdown
## Related
<!-- wingman-links:start -->
- Refs #<SPEC_ID>
- Closes #<TASK_ID>
- Closes #<TASK_ID>
<!-- wingman-links:end -->
```

Rules:

- The governing spec or parent issue always uses `Refs #<SPEC_ID>`.
- Child implementation issues always use `Closes #<TASK_ID>`.
- Emit one line per issue. Do not combine multiple issue numbers on one line.
- Keep the block sorted: spec first, then task issues in ascending numeric order.
- If the markers already exist, replace only the content between them.
- If the markers do not exist, insert the entire `## Related` section after `## Summary` when present, otherwise prepend it to the PR body.

## Deterministic Procedure

### Step 1: Resolve the governing spec issue ID

Normalize `{{spec_reference}}` into `SPEC_ID` using this order:

1. If it matches `#123` or `123`, use `123`
2. If it is a GitHub issue URL, extract the trailing numeric issue number
3. Otherwise, use the raw value

Then verify it with:

```text
issues_get({ id: SPEC_ID, include_body: true })
```

If that fails, STOP and report the unresolved spec reference.

### Step 2: Resolve child implementation issues

Use the canonical spec ID from Step 1.

First try:

```text
issues_list({ parentTaskId: SPEC_ID })
```

If that returns no child issues, fall back to:

```text
issues_list({ project: "{{spec_reference}}" })
```

From the results:

- include only child records whose type is `issue`
- keep both open and closed tasks; this is a PR-link reconciliation step, not a status filter
- sort numerically by issue ID when the IDs are numeric

### Step 3: Rewrite the machine-managed PR body block

Fetch the current body:

```bash
gh pr view {{pr_number}} --json body --jq '.body'
```

Construct the exact `## Related` block from:

- `Refs #SPEC_ID`
- one `Closes #TASK_ID` line per child issue

Then update the PR body so the machine-managed block is present exactly once and matches the canonical issue set.

### Step 4: Reconcile GitHub links idempotently

After the PR body is updated, call:

```text
issues_link_pr({ issueId: SPEC_ID, prNumber: "{{pr_number}}", linkType: "references" })
```

Then call, for each child implementation issue:

```text
issues_link_pr({ issueId: TASK_ID, prNumber: "{{pr_number}}" })
```

Task issues must use the default closing link type. This tool is idempotent. Treat `already_linked` as success.

## Output

Return a concise summary including:

- resolved spec issue ID
- child task issue IDs
- whether the PR body block was inserted or updated
- how many issue links were added versus already present
