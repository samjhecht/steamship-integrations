# Example: Simple Feature (No E2E)

> **Note:** These examples use numeric issue IDs (GitHub provider format). For the markdown provider, substitute prefixed IDs (e.g., `ISS-000042` instead of `42`).

This example shows implementing a simple backend feature that doesn't require E2E testing.

## Scenario

Implementing authentication system from spec #42.

## Workflow

### INIT Phase

```bash
# Session start
session_start(specFile: "42")

# Response:
{
  sessionId: "2025-02-02-abc123",
  worktreePath: "/Users/user/project/.worktrees/spec-42-auth",
  branchName: "wingman/spec-42/2025-02-02-abc123",
  auditPath: ".wingman/sessions/2025-02-02-abc123"
}

# Verify worktree
cd /Users/user/project/.worktrees/spec-42-auth && \
  git branch --show-current
# Output: wingman/spec-42/2025-02-02-abc123
# VERIFIED
```

### PLAN Phase

```bash
# Invoke writing-plans skill (via subagent)
# writing-plans creates MCP issues:
# - 42: Implement JWT token generation
# - 43: Implement token refresh endpoint
# - 44: Add rate limiting middleware
# - 45: Implement user authentication flow
# - 46: Add authentication middleware

# Create GitHub PR
cd /Users/user/project/.worktrees/spec-42-auth && \
gh pr create \
  --title "feat: authentication system" \
  --body "Implements spec #42. See .wingman/issues/ for task details." \
  --draft

# Output: https://github.com/org/repo/pull/123
```

### REVIEW Phase

```bash
# Read plan file coverage analysis
# .wingman/plans/2025-02-02-PLAN_42.md contains:
#
# ## Acceptance Criteria Coverage
# Total AC: 5
# AC covered by tasks: 5 (100%)
# Estimated post-execution compliance: 100%

# Coverage validation: 100% >= 95% -> PASS
# Proceed to EXECUTE automatically (no user input required)
```

**Example: Coverage Gap Detected**

```bash
# If plan only created 4 tasks but spec has 5 AC:
#
# REVIEW Phase: Planning Coverage Gap Detected
#
# Coverage: 80% (4/5 acceptance criteria covered)
#
# Missing AC:
# - AC-005: Middleware protects endpoints
#
# Options:
# a) Auto-create missing tasks (Recommended)
# b) Proceed anyway (risks VERIFY failure)
# c) Abort and replan from scratch
#
# > User chooses (a)
#
# Creating 47: Add authentication middleware
# Updated coverage: 100%
# Proceeding to EXECUTE...
```

### EXECUTE Phase

```bash
# Orchestrator iterates over each issue, dispatching a subagent per issue:

# --- Issue 1 ---
# issues_update(id: "42", status: "in_progress")
# Dispatch subagent: "Implement 42: Implement JWT token generation"
# Subagent follows implementing-issue skill:
#   - Reads issue, writes failing test, implements, refactors
#   - Requests code review, fixes Critical/Important issues
#   - Commits work, reports back
# Verify response: VERIFIED, tests passing, TDD certified, commit a1b2c3d
# session_checkpoint(tasksCompleted: ["42"], tasksPending: ["43","44","45","46"])
# issues_mark_complete(id: "42")

# --- Issue 2 ---
# issues_update(id: "43", status: "in_progress")
# Dispatch subagent: "Implement 43: Implement token refresh endpoint"
# ... TDD -> Code Review -> Approved -> Complete
# session_checkpoint(tasksCompleted: ["42","43"], tasksPending: ["44","45","46"])
# issues_mark_complete(id: "43")

# --- Issue 3 ---
# issues_update(id: "44", status: "in_progress")
# Dispatch subagent: "Implement 44: Add rate limiting middleware"
# Subagent reports blocker: rate limit threshold unclear
# -> ESCALATE to user -> User clarifies -> Re-dispatch -> Complete
# session_checkpoint(...)
# issues_mark_complete(id: "44")

# --- Issue 4 ---
# (same pattern for 45)

# --- Issue 5 ---
# (same pattern for 46)

# Result: All 5 tasks complete
# session_phase(phase: "execute", status: "complete", metadata: {tasks_completed: 5, total_commits: 5})
```

### VERIFY Phase

```bash
# LLM extracts acceptance criteria from spec #42:
# - AC-001: JWT tokens generated with correct claims
# - AC-002: Token refresh works within expiry window
# - AC-003: Rate limiting blocks excessive requests
# - AC-004: Authentication flow redirects correctly
# - AC-005: Middleware protects endpoints

# Verify evidence for each criterion
# AC-001: Test: auth.test.ts:15, Code: auth.ts:42, Commit: a1b2c3d
# AC-002: Test: tokens.test.ts:8, Code: tokens.ts:30, Commit: e4f5g6h
# AC-003: Test: middleware.test.ts:22, Code: middleware.ts:18, Commit: i7j8k9l
# AC-004: Test: flow.test.ts:10, Code: flow.ts:55, Commit: m0n1o2p
# AC-005: Test: middleware.test.ts:40, Code: middleware.ts:70, Commit: q3r4s5t

# Compliance: 5/5 = 100%

# Run test suite
cd /Users/user/project/.worktrees/spec-42-auth && npm test
# Output: 42 tests, 42 passing

# Check git status
cd /Users/user/project/.worktrees/spec-42-auth && git status --short
# Output: (empty - clean)

# Quality gate: 100% >= 95% -> PASS
# Proceed to PUBLISH
```

**Example: Self-Healing Workflow**

```bash
# If initial compliance was 92% (missing edge case test):
#
# Initial audit: 92% (4.6/5 AC met)
# Gap: AC-003 missing edge case test for rate limit threshold
#
# Gap categorization: AUTO_FIX_TEST
#
# Self-healing iteration 1:
# Creating 47: Add edge case test for rate limit threshold
# Dispatching subagent for 47 (follows implementing-issue skill)
# Committed: xyz789
#
# Re-audit: 96% (4.8/5 AC met)
# Quality gate: 96% >= 95% -> PASS
#
# PR updated with self-healing note:
# "During verification, auto-fixed:
# - Created 47: Added edge case test for rate limit threshold
# Final compliance: 96%"
#
# Proceed to PUBLISH
```

**Example: Escalation for Fundamental Gap**

```bash
# If compliance was 85% due to missing core feature:
#
# Initial audit: 85% (4.25/5 AC met)
# Gap: AC-002 token refresh endpoint not implemented
#
# Gap categorization: FUNDAMENTAL_GAP
#
# VERIFY Phase: Compliance Below Threshold
#
# Current compliance: 85%
# Quality gate: 90% required
#
# Self-healing attempted:
# - No auto-fixable gaps found
# - Remaining gaps: 1 FUNDAMENTAL_GAP
#
# Gap Analysis:
# - FUNDAMENTAL_GAP: Token refresh endpoint not implemented (AC-002)
#
# Options:
# 1. Review and approve current implementation (partial delivery)
# 2. Let me create additional tasks for remaining gaps
# 3. Abort session and revisit planning
#
# Your decision?
```

### PUBLISH Phase

```bash
# Update PR description with final summary
cd /Users/user/project/.worktrees/spec-42-auth && \
gh pr edit 123 --body "$(cat << EOF
## Summary

Implements specification: spec #42

### Changes

- Implement JWT token generation
- Implement token refresh endpoint
- Add rate limiting middleware
- Implement user authentication flow
- Add authentication middleware

### Test Results

- All tests passing (42 tests)
- Compliance: 100%

### Tasks Completed

- [x] 42: Implement JWT token generation (a1b2c3d)
- [x] 43: Implement token refresh endpoint (e4f5g6h)
- [x] 44: Add rate limiting middleware (i7j8k9l)
- [x] 45: Implement user authentication flow (m0n1o2p)
- [x] 46: Add authentication middleware (q3r4s5t)

### Implementation Details

- TDD compliance: All functions certified
- Code review: All tasks approved

---

**Session ID:** 2025-02-02-abc123
**Audit trail:** .wingman/sessions/2025-02-02-abc123/

Generated with Claude Code
EOF
)"

# Mark PR ready
cd /Users/user/project/.worktrees/spec-42-auth && \
gh pr ready 123
```

### COMPLETE Phase

```bash
# Complete session
session_complete(
  sessionId: "2025-02-02-abc123",
  status: "completed",
  prUrl: "https://github.com/org/repo/pull/123",
  prNumber: 123,
  summary: "Implemented 5 tasks from spec #42"
)

# Present summary to user
```

## Summary Output

```markdown
## Implementation Complete

**Specification:** spec #42
**PR:** https://github.com/org/repo/pull/123
**Session:** 2025-02-02-abc123

### Summary

| Metric | Value |
|--------|-------|
| Tasks completed | 5/5 |
| Tests passing | 42 |
| Compliance | 100% |
| Code reviews | 5 approved |

### Audit Trail

Location: `.wingman/sessions/2025-02-02-abc123/`
```
