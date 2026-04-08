# Implementation Examples

> **Note:** These examples use numeric issue IDs (GitHub provider format). For the markdown provider, substitute prefixed IDs (e.g., `ISS-000042` instead of `42`).

## Example 1: Standalone Issue Implementation

```
User: /wingman:implementing-issue 42

> Read issue 42 via MCP
> Understand requirements: Add rate limiting to API
> TDD: Write failing test for rate limit behavior
> TDD: Implement rate limiter to pass test
> TDD: Refactor for clarity
> Code review: No critical issues
> Complete: All tests pass, TDD cert provided
```

## Example 2: Dispatched from implementing-specs

```
[Subagent receives issue context from parent]
> Verify working directory
> Read issue 43
> Follow TDD workflow
> Code review finds 1 critical issue
> Fix attempt 1: Success
> Complete: Report results to parent
```

## Example 3: Issue with Blocker

```
User: /wingman:implementing-issue 44

> Read issue 44 via MCP
> Requirements unclear: "Improve performance" with no specific criteria
> ESCALATE: Ask user for specific acceptance criteria
> User provides: "Response time under 200ms for /api/users endpoint"
> TDD: Write failing test asserting <200ms response
> TDD: Implement caching layer to pass test
> Code review: 1 Important issue (missing cache invalidation)
> Fix attempt 1: Add cache invalidation logic -- success
> Complete: All tests pass, TDD cert provided
```
