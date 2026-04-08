---
name: closure-verification
description: Verify that issues referenced in a merged PR were properly closed
---

# Closure Verification

You are verifying that issues referenced in the merged pull request were properly closed.

## Input

- PR number: {{pr_number}}

## Instructions

1. Use `gh pr view {{pr_number}} --json body,title` to read the PR description and title.
2. Extract any issue references (e.g., `#123`, `Fixes #123`, `Closes #123`, `Resolves #123`).
3. For each referenced issue, check if it is closed using `gh issue view <number> --json state`.
4. Report any referenced issues that remain open.

## Output

Return a JSON object:

```json
{
  "referencedIssues": [123, 456],
  "openIssues": [456],
  "allClosed": false
}
```
