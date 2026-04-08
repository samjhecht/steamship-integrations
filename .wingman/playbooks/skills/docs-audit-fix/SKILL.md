---
name: docs-audit-fix
description: Fixes documentation issues on isolated worktrees with PRs. Processes issues from docs-audit-scan in parallel where possible.
---

<purpose>
Process documentation fix issues created by docs-audit-scan, batch them by independence, and dispatch parallel fix agents on isolated worktrees. Each fix agent makes the documentation changes, commits, and creates a PR. This is the second phase of the docs-audit workflow.
</purpose>

<required_context>

## Prerequisites

- Scan results from docs-audit-scan (list of issue IDs)
- Git repository with clean working tree
- `gh` CLI installed and authenticated
- MCP tools available: `issues_get` (with `include_body: true`), `issues_update`, `issues_link_pr`
- Git worktree support for parallel isolation

</required_context>

<available_agent_types>

This is a **workflow skill** -- it coordinates fix subagents dispatched to isolated worktrees.

- **Fix Agent (N instances):** Each fixes one or more non-overlapping documentation issues on its own worktree branch

</available_agent_types>

<process>

<step name="triage" priority="first">

### Phase 1: Triage (Sequential)

**Why sequential:** Need to understand issue contents and determine parallelism before dispatching.

**Task:** Read all scan issues, group by independence, and determine batching strategy.

**Approach:**

**1.1 Fetch Issues**

1. Read the `issue_ids` input to get the list of issue IDs
2. For each issue ID, fetch full details via `issues_get` MCP tool (with `include_body: true`)
3. Extract from each issue:
   - File paths affected
   - The specific changes required
   - Acceptance criteria checklist

**1.2 Analyze Independence**

Determine which issues can be fixed in parallel:

- Issues touching **different files** are independent and can run in parallel
- Issues touching the **same file** must be sequential (or merged into one batch)
- Issues touching files that cross-reference each other should be in the same batch

Build a dependency graph:
```
Issue A: touches docs/01-architecture/overview.md -> Batch 1
Issue B: touches packages/gateway/README.md      -> Batch 2
Issue C: touches docs/01-architecture/overview.md -> Batch 1 (same file as A)
Issue D: touches packages/tui/AGENTS.md           -> Batch 3
```

**1.3 Create Batches**

Group issues into batches where:
- Each batch contains issues that can be fixed together without conflicts
- Batches are maximally parallelizable (minimize batch count)
- Each batch gets a single worktree branch

**Output:** Ordered list of batches, each containing issue IDs and affected file paths.

</step>

---

<step name="fix-dispatch">

### Phase 2: Fix Dispatch (Parallel Where Possible)

**Why parallel:** Independent batches have no file conflicts and can safely run on separate worktrees.

**Task:** Dispatch fix agents for independent batches simultaneously.

For each batch of independent issues, dispatch a fix subagent via the Task tool. All independent batch dispatches should be in a **single message** for true parallelism.

Each fix agent receives these instructions:

**Fix Agent Instructions:**

1. **Create isolated worktree:**
   ```bash
   git worktree add ../worktree-batch-<batch_number> -b docs/fix-batch-<batch_number>-<short_description>
   ```
   All subsequent steps (edits, commits, pushes) are performed **inside the worktree directory** (`../worktree-batch-<batch_number>`).

2. **For each issue in the batch:**
   - Read the issue's acceptance criteria
   - Open the target documentation file within the worktree
   - Make the specified changes:
     - Fix broken file paths
     - Update stale content to match current code
     - Add missing documentation sections
     - Fix formatting inconsistencies
     - Correct API parameter documentation
   - Verify each acceptance criterion is satisfied

3. **Validate changes:**
   - Ensure no broken cross-references were introduced
   - Verify markdown renders correctly (no unclosed tags, valid tables)
   - Check that file paths referenced in changes actually exist

4. **Commit with conventional message (from within the worktree):**
   ```bash
   git commit -m "docs: [concise description of changes]"
   ```
   Use a single commit per batch. If a batch has diverse changes, use a commit body:
   ```
   docs: fix documentation issues from audit

   - Fix broken paths in architecture overview
   - Update API parameter docs for MCP tools
   - Add missing README for gateway package
   ```

5. **Push and create PR (from within the worktree):**
   ```bash
   git push -u origin docs/fix-batch-<batch_number>-<short_description>
   gh pr create --title "docs: fix [description]" --body "Fixes documentation issues found by docs-audit-scan.

   ## Issues Addressed
   - #<issue_1> - <title>
   - #<issue_2> - <title>

   ## Changes
   - <change 1>
   - <change 2>

   Closes #<issue_1>, closes #<issue_2>"
   ```

6. **Link PR to issues:**
   - For each issue in the batch, use `issues_link_pr` MCP tool to associate the PR

7. **Clean up worktree:**
   ```bash
   git worktree remove ../worktree-batch-<batch_number>
   ```

8. **Return result:**
   - PR number
   - List of issue IDs addressed
   - Any issues that could not be fixed (with reason)

**Sequential Batches:**

If batches have file conflicts and cannot be parallelized, run them sequentially. Each sequential batch waits for the previous batch's PR to be created before starting.

</step>

---

<step name="status-report">

### Phase 3: Status Report (Sequential)

**Why sequential:** Needs results from all fix agents.

**Task:** Collect results and produce a summary for the review phase.

**Approach:**

1. Collect all PR numbers from fix agents
2. Collect any failures or skipped issues
3. Produce summary:

```
DOCS_AUDIT_FIX_COMPLETE

PRs created: [count]
Issues addressed: [count]
Issues skipped: [count] (with reasons)

PR list:
  #<pr_number> - <title> (fixes #<issue_1>, #<issue_2>)
  #<pr_number> - <title> (fixes #<issue_3>)

Skipped issues:
  #<issue_id> - <reason>
```

**Output:** The fix result object containing:
- `prNumbers`: array of created PR numbers
- `issuesAddressed`: array of issue IDs that were fixed
- `issuesSkipped`: array of `{ issueId, reason }` for any issues that could not be fixed
- `summary`: the summary report text

</step>

</process>

<success_criteria>
- [ ] All scan result issue IDs fetched and analyzed for independence
- [ ] Issues grouped into maximally parallel batches
- [ ] Fix agents dispatched on isolated worktrees (independent batches in single message)
- [ ] Each fix agent made correct documentation changes
- [ ] Changes committed with conventional commit messages
- [ ] PRs created and linked to source issues
- [ ] Status report produced with PR numbers and fix summary
</success_criteria>

<error_handling>

## Failure Modes

**Fix agent encounters merge conflict:**
- Skip the conflicting file
- Report the conflict in the agent's output
- Include the issue in `issuesSkipped` with reason "merge conflict"

**Issue requirements are ambiguous:**
- Make best-effort fix based on available information
- Add a PR comment noting the ambiguity for reviewer attention
- Do not block the batch on one ambiguous issue

**Worktree creation fails (`git worktree add` errors):**
- Check if a stale worktree exists at the target path and remove it with `git worktree remove --force`
- If the branch already exists, retry with a unique suffix (e.g., `-retry1`)
- If worktrees are fundamentally broken (e.g., filesystem permissions), fall back to sequential branch-based workflow (`git checkout -b`, fix, commit, `git checkout -`) and note reduced parallelism in the status report

**Too many batches (10+):**
- Cap parallel dispatch at 5 concurrent fix agents
- Run remaining batches in a second wave after the first completes

</error_handling>
