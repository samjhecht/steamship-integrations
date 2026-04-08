---
name: implement-plan
description: Implements an existing plan by executing MCP issues in a worktree with TDD and PR publication. Use when a plan with subtask issues already exists and you need to execute it. Does NOT create plans -- use writing-plans for that.
---

{{#if spec_reference}}
## Workflow Context
Specification: {{spec_reference}}
{{/if}}
{{#if issue_ids}}
Issue IDs to implement: {{issue_ids}}
{{/if}}

<purpose>

# Implement Plan

## Overview

Executes an existing implementation plan -- representing a set of task issues -- in an isolated git worktree. Handles worktree creation, batched subagent dispatch with TDD, and PR publication.


## When to Use / When NOT to Use

**Use when:**
- A plan with implementation task issues already exists (via `writing-plans` or manually)
- You need worktree isolation, TDD enforcement, and PR publication

**Do NOT use when:**
- No issues exist yet -- use `writing-plans` first
- Implementing a single issue -- use `implementing-issue` directly
- Need end-to-end spec-to-PR including planning -- use `implementing-specs`

</purpose>

<required_context>

## Inputs

| Variable | Required | Description |
|----------|----------|-------------|
| `{SPEC_REFERENCE}` | Yes | GitHub issue number, URL, or spec identifier (e.g., `#42`, `SPEC-000006`) |
| `{ISSUE_IDS}` | No | JSON array of issue IDs to implement (e.g., `["42", "43"]` for GitHub or `["ISS-000042", "ISS-000043"]` for markdown) |

If `{ISSUE_IDS}` not provided, discover via `issues_list(project: "{SPEC_REFERENCE}")`. Filter to `open` or `in_progress` status.

## MCP Tool Requirements

| Tool | Phase | Purpose |
|------|-------|---------|
| `issues_metadata` | INIT | Verify MCP availability |
| `issues_list` | EXECUTE | Find issues for spec reference (by project) |
| `issues_get` | EXECUTE | Read full issue details (use `include_body: true`) |
| `issues_mark_complete` | EXECUTE | Mark completed issues |
| `issues_all_complete` | EXECUTE | Verify all issues done |
| `issues_create` | EXECUTE | Create supplemental issues for blockers |

</required_context>

<available_agent_types>

| Agent | Purpose |
|-------|---------|
| implementer-subagent | Implements a single issue with TDD in the worktree via the implementing-issue skill |

</available_agent_types>

## Workflow Context Detection

**Before creating a worktree**, check if you are already running inside a workflow worktree:

If the value `{{in_workflow_worktree}}` is non-empty (not literally `{{in_workflow_worktree}}`), you are already in an isolated worktree at that path. Do NOT create another one -- work directly in the current branch.

If the value is empty or still shows the literal template syntax, proceed with normal worktree creation.

## Workflow

```
INIT -> EXECUTE -> PUBLISH -> COMPLETE
```

<process>

---

<step name="init" priority="first">

## Phase 1: INIT

**Skip this phase if you are already in a workflow worktree** (see Workflow Context Detection above).

Create isolated worktree, bootstrap commit, and draft PR.

### Step 1: Verify MCP Availability

```
issues_metadata()
```

If this fails, STOP: "MCP server is not available. Verify the wingman plugin is active."

### Step 2: Derive Branch Name

```bash
SPEC_REF='{SPEC_REFERENCE}'
BRANCH_SLUG=$(echo "$SPEC_REF" | tr '[:upper:]' '[:lower:]' | sed 's/[^a-z0-9-]/-/g' | sed 's/--*/-/g' | sed 's/^-//;s/-$//')
BRANCH_NAME="feat/$BRANCH_SLUG"
```

Use `BRANCH_SLUG` (not raw `{SPEC_REFERENCE}`) in commit messages and shell operations.

### Step 3: Create and Verify Worktree

```bash
REPO_ROOT="$(git rev-parse --show-toplevel)"
WORKTREE_PATH="$REPO_ROOT/../worktrees/$BRANCH_SLUG"
git worktree add -b "$BRANCH_NAME" "$WORKTREE_PATH" main
WORKTREE_ABSOLUTE="$(cd "$WORKTREE_PATH" && pwd)"
```

Verify:
```bash
cd "$WORKTREE_ABSOLUTE" && \
  test "$(git branch --show-current)" = "$BRANCH_NAME" && echo "VERIFIED" || echo "FAILED"
```

**If FAILED, STOP and report error.**

### Step 4: Bootstrap and Draft PR

```bash
cd "$WORKTREE_ABSOLUTE" && \
  git commit --allow-empty -m "chore: init implementation for $BRANCH_SLUG" && \
  git push -u origin "$BRANCH_NAME"
```

```bash
cd "$WORKTREE_ABSOLUTE" && \
  gh pr create \
    --title "feat: implement $BRANCH_SLUG" \
    --body "$(cat <<'EOF'
## Summary
Implements {SPEC_REFERENCE}.

## Related
<!-- wingman-links:start -->
- Refs #<SPEC_ID>
<!-- wingman-links:end -->

## Status
Draft -- implementation in progress.
EOF
)" \
    --draft --base main --head "$BRANCH_NAME"
```

Capture `PR_URL` and `PR_NUMBER` from output.

<gate type="yes-no">

### Quality Gate

Worktree on correct branch. Bootstrap commit pushed. Draft PR created.

</gate>

</step>

---

<step name="execute">

## Phase 2: EXECUTE

Batch dispatch of implementer subagents with TDD.

### Step 1: Collect Issues

If `{ISSUE_IDS}` not provided, run `issues_list(project: "{SPEC_REFERENCE}")`. For each ID, read via `issues_get(id: "{ISSUE_ID}", include_body: true)`. Filter out `closed`/`cancelled`. Set `TASK_COUNT`.

### Step 2: Classify Dependencies

Analyze issues and assign to ordered batches:

- **INDEPENDENT**: Different files, no shared types/APIs/test files
- **DEPENDENT**: Same files, produces types the other needs, must sequence

**When in doubt, classify as DEPENDENT.** False sequentiality is safe; false parallelism causes conflicts.

```
BATCH 1 (parallel): {ISSUE_A}, {ISSUE_B}
BATCH 2 (after batch 1): {ISSUE_C} (depends on {ISSUE_A})
```

### Step 3: Execute Batches

For each batch: dispatch subagents (parallel if 2+, max 4 concurrent).

**Subagent prompt template:**

````markdown
You are implementing issue #{ISSUE_ID} as part of a coordinated plan execution.

## Working Directory

**Path:** {WORKTREE_ABSOLUTE}
**Branch:** {BRANCH_NAME}

### MANDATORY: Verify Location First

```bash
cd {WORKTREE_ABSOLUTE} && \
  test "$(pwd)" = "{WORKTREE_ABSOLUTE}" && echo "VERIFIED" || echo "FAILED - STOP"
```

ALL bash commands MUST start with: `cd {WORKTREE_ABSOLUTE} && `

## Implementation

Use the implementing-issue skill to implement this issue:

```
/wingman:implementing-issue {ISSUE_ID}
```

The skill handles TDD workflow, test authenticity, autonomy boundaries, deviation documentation, code review, and completion reporting.

## Completion

After the skill completes, commit ONLY files you changed (never `git add -A` or `git add .`):
```bash
cd {WORKTREE_ABSOLUTE} && git add [specific files] && git commit -m "feat: [message]"
```

Report: implementation summary, test results, commit hash, and any blockers or deviations.
````

### Step 4: Post-Batch Verification

After each batch:

1. **Run test suite:** `cd {WORKTREE_ABSOLUTE} && pnpm test 2>&1`
2. **Check conflicts:** `cd {WORKTREE_ABSOLUTE} && git status --short`
3. **If tests fail:** Identify which issue caused it, escalate with batch context and test output
4. **If tests pass:** Mark issues complete via `issues_mark_complete(id: "{ISSUE_ID}")`, push changes

### Step 5: Finalize

Push all changes. Verify via `issues_all_complete(project: "{SPEC_REFERENCE}")`.

<gate type="yes-no">

### Quality Gate

All issues complete, tests passing. Escalate blockers to user.

</gate>

</step>

---

<step name="publish">

## Phase 3: PUBLISH

Push final state, update PR, and mark ready.

### Step 1: Push and Update PR

```bash
cd "{WORKTREE_ABSOLUTE}" && git push
```

Update PR body with results:
```bash
cd "{WORKTREE_ABSOLUTE}" && \
gh pr edit {PR_NUMBER} --body "$(cat <<'EOF'
## Summary
Implements {SPEC_REFERENCE}.

## Related
<!-- wingman-links:start -->
- Refs #<SPEC_ID>
- Closes #<TASK_ID>
<!-- wingman-links:end -->

## Issues Implemented
| Issue | Title | Status |
|-------|-------|--------|
| {ISSUE_ID} | {ISSUE_TITLE} | Complete |

## Tests
- **Result:** {TESTS_PASSED}/{TESTS_TOTAL} passing

## Changes
{GIT_DIFF_STAT}
EOF
)"
```

The `## Related` block is machine-managed.

Rules:

- Preserve the `<!-- wingman-links:start -->` / `<!-- wingman-links:end -->` markers exactly.
- The spec line must remain a non-closing reference.
- Emit one `Closes #<TASK_ID>` line per implementation issue.
- Do not merge multiple task IDs onto one line.
- When the governing spec is a numeric GitHub issue, render it as `Refs #<SPEC_ID>` rather than raw prose.
- If the issue set changes during execution, regenerate the block so it matches the canonical task list before leaving PUBLISH.

### Step 2: Mark Ready

```bash
cd "{WORKTREE_ABSOLUTE}" && gh pr ready {PR_NUMBER}
```

Report: PR URL, branch, issue count, test results.

</step>

</process>

---

## Output Contract

The workflow engine captures structured output via the agent definition's `outputSchema` (PublishResultSchema). The implementer agent must return: `prNumber` (number), `prUrl` (string URL), `branchName` (string), `commitCount` (number), `summary` (string). This is enforced at the SDK level -- no manual JSON formatting needed.

<success_criteria>
- [ ] Worktree created on correct branch with bootstrap commit pushed
- [ ] Draft PR created and PR number captured
- [ ] All issues collected, classified into dependency batches, and dispatched
- [ ] Each issue implemented with TDD (RED-GREEN-REFACTOR cycle)
- [ ] All tests passing after each batch and at final completion
- [ ] All issues marked complete via `issues_all_complete`
- [ ] PR body updated with implementation summary and test results
- [ ] PR marked ready for review
</success_criteria>

---

<error_handling>

## Error Handling

| Scenario | Recovery |
|----------|----------|
| **Worktree creation fails** | Check if branch exists (`git branch --list`), ask user to reuse or rename |
| **Subagent: unclear requirements** | Escalate to user immediately |
| **Subagent: git conflicts** | Escalate to user, do not auto-resolve |
| **Subagent: test failures after 2 attempts** | Escalate with test output |
| **MCP server unavailable mid-execution** | Push committed work, report last state, provide resume instructions |
| **Draft PR creation fails** | Check `gh auth status`, check if PR exists via `gh pr list --head "$BRANCH_NAME"` |
| **Tests fail after parallel batch** | Identify causal issue via commit analysis, suggest sequential re-run |

</error_handling>
