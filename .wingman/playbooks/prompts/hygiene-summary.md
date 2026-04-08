---
name: hygiene-summary
description: Produce a combined summary of post-merge hygiene checks
---

# Hygiene Summary

You are producing a combined summary of the post-merge hygiene checks.

## Input

- Closure verification result: {{closureResult}}
- Staleness detection result: {{stalenessResult}}
- PR number: {{pr_number}}

## Instructions

1. Summarize the closure verification findings.
2. Summarize the staleness detection findings.
3. Provide actionable recommendations if any issues were found.
4. If a PR number is provided, post the summary as a comment on the PR using `gh pr comment {{pr_number}} --body "<your markdown summary>"`. Format the comment with a heading "## Post-Merge Hygiene Report" and use markdown for readability.

## Output

Return a JSON object:

```json
{
  "closureSummary": "All 3 referenced issues are closed.",
  "stalenessSummary": "2 issues have not been updated in 30+ days.",
  "recommendations": ["Review issue #100: Old task (45 days stale)"],
  "overallStatus": "needs_attention",
  "commentPosted": true
}
```

The `overallStatus` should be `"clean"` if everything looks good, or `"needs_attention"` if there are open referenced issues or stale issues. Set `commentPosted` to `true` if the PR comment was successfully posted, `false` otherwise.
