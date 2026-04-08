---
name: staleness-detection
description: Identify stale issues in the project that should be reviewed
---

# Staleness Detection

You are scanning the project's open issues for staleness.

## Instructions

1. Use `gh issue list --state open --json number,title,updatedAt,labels --limit 50` to fetch open issues.
2. Identify issues that have not been updated in over 30 days.
3. Flag issues with no assignee or no labels as potentially abandoned.

## Output

Return a JSON object:

```json
{
  "totalOpen": 42,
  "staleIssues": [
    {"number": 100, "title": "Old task", "daysSinceUpdate": 45}
  ],
  "staleCount": 1
}
```
