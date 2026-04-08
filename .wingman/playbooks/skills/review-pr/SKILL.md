---
description: Use when reviewing a pull request end-to-end -- dispatches parallel reviewer subagents (code-quality, security, testing, completeness), consolidates findings, posts results to the PR, fixes critical and warning issues, and posts a follow-up summary. Accepts PR number, URL, branch, or defaults to the current branch.
---

{{#if pr_number}}
## Workflow Context
PR to review: {{pr_number}}
{{/if}}

# Review PR

<purpose>
Orchestrates a modular, parallelized pull request review. Four specialized reviewer subagents analyze the PR independently, their findings are consolidated and posted to the PR, and fixable issues are resolved automatically.
</purpose>

<required_context>

## Input Variables

When invoked from a workflow, the following variables may be pre-populated:
- `{{pr_number}}` -- The PR number to review (e.g., from the implement phase's structured output)

If `pr_number` is provided via workflow context, use it directly as the PR identifier instead of searching for a PR on the current branch.

## Arguments

- **PR identifier** (positional, optional): A PR number, URL, branch name, or empty to use the current branch.
- **--type** (optional): One of `code-quality`, `security`, `testing`, `completeness`, or `all` (default: `all`). When a single type is specified, only that reviewer is dispatched.

</required_context>

<available_agent_types>

| Reviewer | subagent_type |
|----------|---------------|
| code-quality | `code-quality-reviewer` |
| security | `security-reviewer` |
| testing | `testing-reviewer` |
| completeness | `completeness-reviewer` |

</available_agent_types>

<process>

<step name="context-gathering" priority="first">

## Phase 1: Context Gathering

### Step 1: Resolve the PR

Determine the target PR. The user may have provided a PR number, a GitHub URL, a branch name, or nothing at all. Determine which case applies:

- **PR number** (e.g. `42`, `#42`): Use it directly.
- **GitHub URL** (e.g. `https://github.com/owner/repo/pull/42`): Extract the owner, repo, and number from the URL.
- **Branch or PR name** (e.g. `feat/new-thing`): Use `gh pr list --search "<input>" --json number,title,headRefName --limit 5` to find a match.
- **Nothing provided**: Try these in order:
  1. Check the current branch: `gh pr view --json number,url 2>/dev/null`
  2. Look at recent conversation context for PR references (URLs, numbers, branch names)
  3. If still nothing, list recent open PRs with `gh pr list --limit 10` and ask the user which one.

```bash
gh pr view {PR_IDENTIFIER} --json number,title,body,headRefName,baseRefName,url,author
```

Capture:
- `{PR_NUMBER}` -- the PR number
- `{PR_TITLE}` -- the PR title
- `{PR_BODY}` -- the PR description body
- `{HEAD_BRANCH}` -- the head (feature) branch
- `{BASE_BRANCH}` -- the base branch
- `{PR_URL}` -- the PR URL
- `{PR_AUTHOR}` -- the PR author login

If `gh pr view` fails, stop and report the error. Do not guess.

### Step 2: Get the diff and changed files

```bash
gh pr diff {PR_NUMBER}
gh pr view {PR_NUMBER} --json files --jq '.files[].path'
```

Capture:
- `{DIFF}` -- the full diff output
- `{CHANGED_FILES}` -- newline-separated list of changed file paths

### Step 3: Look for a linked specification

Search for a spec reference in the PR body, branch name, and commit messages. Look for both `SPEC-NNNNNN` patterns and GitHub issue references (`#N`, `Fixes #N`, `Closes #N`):

```bash
gh pr view {PR_NUMBER} --json body,headRefName,commits --jq '
  [.body, .headRefName, (.commits[].messageHeadline)] | join("\n")
' | grep -oE '(SPEC-[0-9]+|#[0-9]+)' | head -5
```

If a SPEC identifier or issue reference is found, read its content:

```bash
gh issue view {SPEC_OR_ISSUE_NUMBER} --json title,body
```

Capture `{SPEC_CONTENT}` (title + body), or empty string if no spec is linked.

</step>

<step name="dispatch-reviewers">

## Phase 2: Dispatch Reviewers

### Step 1: Determine which reviewers to dispatch

If `--type` was specified and is not `all`, dispatch only that single reviewer. Otherwise dispatch all four:

| Reviewer | Prompt file |
|----------|------------|
| code-quality | `.wingman/playbooks/prompts/reviews/code-quality.md` |
| security | `.wingman/playbooks/prompts/reviews/security.md` |
| testing | `.wingman/playbooks/prompts/reviews/testing.md` |
| completeness | `.wingman/playbooks/prompts/reviews/completeness.md` |

### Step 2: Build the reviewer prompt for each type

For each reviewer, assemble the prompt from these parts in order:

1. **Base preamble** -- Read `.wingman/playbooks/prompts/system/reviewer.md` and use its content (skip the YAML frontmatter, use only the body).

2. **Review-type-specific prompt** -- Read the review type's prompt file from `.wingman/playbooks/prompts/reviews/{REVIEW_TYPE}.md`. Extract the body (skip the YAML frontmatter).

3. **PR context** -- Append the following block:

```
## Pull Request Context

PR #{PR_NUMBER}: {PR_TITLE}
Author: {PR_AUTHOR}
URL: {PR_URL}
Branch: {HEAD_BRANCH} -> {BASE_BRANCH}

Description:
{PR_BODY}

Linked Specification:
{SPEC_CONTENT}

Changed Files:
{CHANGED_FILES}

Diff:
{DIFF}
```

### Step 3: Dispatch subagents in parallel

Use the Agent tool to dispatch one subagent per reviewer. All subagents run in parallel. Use the following `subagent_type` values so that the correct rule profiles are injected automatically:

| Reviewer | subagent_type |
|----------|---------------|
| code-quality | `code-quality-reviewer` |
| security | `security-reviewer` |
| testing | `testing-reviewer` |
| completeness | `completeness-reviewer` |

Each subagent:
- Receives the assembled prompt from Step 2
- Has **read-only tools only**: Read, Glob, Grep
- Must return structured JSON matching the output format defined in the review-type prompt
- Has a **5-minute timeout** -- if a subagent does not return within 5 minutes, treat it as a timeout and record zero findings for that review type with a note that the review timed out

If `--type` specifies a single reviewer, dispatch only that one subagent (no parallelism needed).

</step>

<step name="consolidation">

## Phase 3: Consolidation

### Step 1: Read the consolidation prompt

Read `.wingman/playbooks/prompts/reviews/consolidate.md` for consolidation instructions. If the file does not exist, use the default logic below.

### Step 2: Merge and deduplicate findings

Collect the JSON results from all reviewer subagents. Merge all findings into a single list:

1. **Deduplicate**: If two or more findings reference the same file and line (or overlapping line ranges) with the same underlying issue, keep only the highest-severity instance. Preserve the most specific description and fix suggestion.

2. **Sort by severity**: Order findings as `critical` first, then `warning`, then `nit`.

3. **Merge strengths**: Combine all strengths lists, removing exact duplicates.

4. **Determine verdict**:
   - `MAJOR REVISION` if any reviewer returned `fail`, OR there are 3 or more critical findings
   - `NEEDS FIXES` if any reviewer returned `warn`, OR there are any critical findings, OR there are 5 or more warning findings
   - `PASS` if all reviewers returned `pass` and no critical or excessive warning findings

### Step 3: Capture consolidated results

Capture:
- `{VERDICT}` -- `PASS`, `NEEDS FIXES`, or `MAJOR REVISION`
- `{CRITICAL_COUNT}` -- number of critical findings
- `{WARNING_COUNT}` -- number of warning findings
- `{NIT_COUNT}` -- number of nit findings
- `{FINDINGS_CRITICAL}` -- list of critical findings
- `{FINDINGS_WARNINGS}` -- list of warning findings
- `{FINDINGS_NITS}` -- list of nit findings
- `{STRENGTHS}` -- merged strengths list
- `{REVIEW_TYPES}` -- comma-separated list of review types that ran

</step>

<step name="post-findings">

## Phase 4: Post Findings

### Step 1: Format the findings comment

Build a GitHub comment in this exact format:

```markdown
## Review Findings

**Verdict:** {VERDICT}
**Summary:** {CRITICAL_COUNT} critical, {WARNING_COUNT} warnings, {NIT_COUNT} nits across {REVIEW_TYPES}

### Critical
| # | Type | File | Line | Finding | Fix |
|---|------|------|------|---------|-----|
| 1 | {REVIEW_TYPE} | `{FILE}` | {LINE} | {DESCRIPTION} | {FIX} |

### Warnings
| # | Type | File | Line | Finding | Fix |
|---|------|------|------|---------|-----|
| 1 | {REVIEW_TYPE} | `{FILE}` | {LINE} | {DESCRIPTION} | {FIX} |

### Nits
<details>
<summary>{NIT_COUNT} nits (click to expand)</summary>

| # | Type | File | Line | Finding | Fix |
|---|------|------|------|---------|-----|
| 1 | {REVIEW_TYPE} | `{FILE}` | {LINE} | {DESCRIPTION} | {FIX} |

</details>

### Strengths
- {STRENGTH_1}
- {STRENGTH_2}

---
*Reviewed by wingman ({REVIEW_TYPES})*
```

Rules for the comment:
- Omit any severity section that has zero findings (do not render an empty table).
- If the total comment exceeds 60,000 characters, truncate nits first (keep only the first 10), then truncate warnings (keep only the first 20). Append a note: `[Truncated: {N} additional findings omitted due to size limits]`.
- Nits are always inside a collapsed `<details>` block.

### Step 2: Post the comment

```bash
gh pr comment {PR_NUMBER} --body "{FORMATTED_COMMENT}"
```

Always use a heredoc or temp file — never interpolate `{FORMATTED_COMMENT}` directly into a quoted string argument. PR diffs and descriptions can contain backticks, dollar signs, and nested quotes that will break inline shell interpolation.

### Step 3: Check verdict

<gate type="yes-no">
If the verdict is `PASS`, stop here. The review is complete. Report the result.

If the verdict is `NEEDS FIXES` or `MAJOR REVISION`, proceed to Phase 5.
</gate>

</step>

<step name="fix-issues">

## Phase 5: Fix Issues

**IMPORTANT: All code modifications in this phase MUST happen in an isolated git worktree. Never modify files, stage changes, or switch branches in the user's current checkout.**

### Step 1: Create isolated worktree

Create a worktree at the same commit as `{HEAD_BRANCH}`. Use `--detach` so this works regardless of which branch is currently checked out in the main tree:

```bash
WORKTREE_PATH="../$(basename $(pwd))-review-fixes-{PR_NUMBER}"
git fetch origin {HEAD_BRANCH}
git worktree add --detach "$WORKTREE_PATH" origin/{HEAD_BRANCH}
git -C "$WORKTREE_PATH" checkout -b wingman-review-fixes-{PR_NUMBER}
```

Capture `{WORKTREE_PATH}` as the **absolute path** of the worktree directory (resolve it with `realpath` or `pwd`). All subsequent file operations in this phase use this path.

### Step 2: Group fixable findings by file

Collect all `critical` and `warning` findings. Group them by file path. Each group becomes a fix task.

Skip any findings that:
- Have no clear file location
- Require architectural changes spanning many files
- Are flagged as "needs human decision" in their description

### Step 3: Dispatch parallel fix subagents

For each file group, dispatch a fix subagent using the Agent tool. Fix subagents run in parallel (one per file group).

Each fix subagent receives:
- **Full tool access** (Read, Write, Edit, Bash, Glob, Grep -- not read-only)
- The list of findings for its assigned file(s)
- The original diff context for those files
- **The worktree path**: `{WORKTREE_PATH}`
- Instructions to:
  1. Read the current state of the file(s) using absolute paths under `{WORKTREE_PATH}`
  2. Apply fixes for each finding
  3. Verify the fix does not introduce new issues

**CRITICAL**: Every file path in the subagent prompt must be an absolute path under `{WORKTREE_PATH}`. For example, if a finding references `src/components/Foo.tsx`, the subagent must operate on `{WORKTREE_PATH}/src/components/Foo.tsx`.

The fix subagent prompt:

```
You are working in an isolated worktree at {WORKTREE_PATH}.
All file operations MUST use absolute paths under this directory.

Fix the following review findings:

{FINDINGS_FOR_FILE}

For each finding:
1. Read the file at {WORKTREE_PATH}/{FILE_PATH} and understand the surrounding context
2. Apply the suggested fix (or an equivalent correct fix) using absolute paths under {WORKTREE_PATH}
3. Verify the change is correct

Return a JSON summary:
{
  "file": "{FILE_PATH}",
  "fixes_applied": [{"finding_number": N, "description": "what was done"}],
  "fixes_skipped": [{"finding_number": N, "reason": "why it was skipped"}]
}
```

### Step 4: Run tests in the worktree

After all fix subagents return, run the relevant test suite **in the worktree**:

```bash
cd {WORKTREE_PATH} && pnpm test
```

Use the appropriate test command for the specific package(s) that were modified. Do NOT run monorepo-wide test commands.

### Step 5: Handle test failures

If tests fail, dispatch a test-fix subagent with full tool access:

```
You are working in an isolated worktree at {WORKTREE_PATH}.
All file operations MUST use absolute paths under this directory.

Tests are failing after review fixes were applied.

Test output:
{TEST_OUTPUT}

Files modified:
{MODIFIED_FILES}

Diagnose the test failure and fix it. The failure may be caused by:
1. A fix that introduced a regression
2. A test that needs updating to match the corrected behavior
3. A pre-existing test issue exposed by the changes

Fix the root cause. Do not disable or skip tests.
Return a JSON summary with fixes_applied[], fixes_skipped[].
```

After the test-fix subagent returns, re-run tests to confirm they pass.

### Step 6: Commit and push from worktree

After all fixes are applied and tests pass, commit and push **from the worktree**:

```bash
git -C {WORKTREE_PATH} add {MODIFIED_FILES}
git -C {WORKTREE_PATH} commit -m "$(cat <<'EOF'
fix: address review findings from wingman review

Apply fixes for {CRITICAL_COUNT} critical and {WARNING_COUNT} warning findings
identified during automated review.

Co-Authored-By: Claude <noreply@anthropic.com>
EOF
)"

git -C {WORKTREE_PATH} push origin HEAD:{HEAD_BRANCH}
```

The `HEAD:{HEAD_BRANCH}` refspec pushes the worktree's current HEAD to the remote PR branch, regardless of the local branch name.

If there are no changes to commit (all findings were skipped), skip this step.

### Step 7: Remove worktree

Clean up the worktree and temporary branch:

```bash
git worktree remove {WORKTREE_PATH} --force
git branch -D wingman-review-fixes-{PR_NUMBER} 2>/dev/null || true
```

Always remove the worktree, even if earlier steps failed. The user's checkout must be left in its original state.

### Step 8: Post follow-up summary

Post a second comment to the PR summarizing what was fixed:

```markdown
## Review Fixes Applied

**Fixes applied:** {FIXED_COUNT} of {TOTAL_FIXABLE_COUNT} findings addressed

### Fixed
| # | File | Finding | What was done |
|---|------|---------|---------------|
| 1 | `{FILE}` | {FINDING_TITLE} | {DESCRIPTION_OF_FIX} |

### Skipped
| # | File | Finding | Reason |
|---|------|---------|--------|
| 1 | `{FILE}` | {FINDING_TITLE} | {REASON} |

### Test Results
All tests passing after fixes.

---
*Fixed by wingman -- commit {SHORT_SHA}*
```

Omit the "Skipped" section if nothing was skipped. Omit "Fixed" if nothing was fixed.

```bash
gh pr comment {PR_NUMBER} --body "{FOLLOW_UP_COMMENT}"
```

### Step 9: Create Follow-On Issues for Unfixed Findings

After posting the follow-up summary, iterate over findings that were skipped in Step 2 (categorized as "architectural", "needs human decision", or "subagent error"):

For each unfixed finding:

1. Create a tracked issue via `issues_create`:
   ```
   issues_create({
     title: "Follow-on: [finding title from review]",
     description: `## From Review

   **PR:** #${PR_NUMBER} (${PR_URL})
   **Reviewer:** ${REVIEW_TYPE}
   **Severity:** ${SEVERITY}

   ## Finding
   ${FINDING_DESCRIPTION}

   ## Suggested Fix
   ${FIX_SUGGESTION}

   ## Context
   File: ${FILE_PATH}
   Line: ${LINE_NUMBER}
   Spec: ${SPEC_REFERENCE || "none linked"}`,
     type: "issue",
     labels: ["follow-on", "from-review"],
     priority: severity === "critical" ? "high" : "medium",
     governanceContext: {
       parentTaskId: "${SPEC_ID}"
     }
   })
   ```

2. If a spec is linked, call `issues_set_parent` to link the follow-on issue to the spec

3. Collect all created issue IDs for the follow-up comment update

After creating all follow-on issues, update the follow-up comment (from Step 8) to include links:

```markdown
### Follow-On Issues Created
| # | Issue | Finding | Priority |
|---|-------|---------|----------|
| 1 | #NNN  | [title] | high     |
```

If no findings were skipped (all were fixed), skip this step entirely.

</step>

<step name="update-pr-summary">

## Phase 6: Update PR Summary

After all review and fix work is complete, update the PR description so it accurately reflects the final state of the PR. The PR summary is the permanent record of what the PR achieved -- review findings and fix details live in the comments and do not need to be restated.

### Step 1: Gather final state

Collect:
- The current PR title and body (`gh pr view {PR_NUMBER} --json title,body`)
- The full list of commits on the branch (`gh pr view {PR_NUMBER} --json commits --jq '.commits[].messageHeadline'`)
- The linked spec or issue identifier (`{SPEC_CONTENT}` from Phase 1, if any)
- The machine-managed PR links block delimited by `<!-- wingman-links:start -->` and `<!-- wingman-links:end -->`, if present
- The review verdict and types that ran

### Step 2: Write the updated PR body

Update the PR body using `gh pr edit {PR_NUMBER} --body`. The body should contain:

```markdown
## Summary

[2-4 sentences describing what this PR achieves. Written from the perspective of "what changed and why", not "what the agent did". Be specific about the user-visible or developer-visible outcomes.]

## Related

<!-- wingman-links:start -->
- Refs #123
- Closes #456
<!-- wingman-links:end -->

- [Any other related PRs or issues]

## Changes

[Bulleted list of the key changes, grouped logically. Not a file-by-file list -- describe the changes at the level a reviewer would care about.]

## Review

- Reviewed by wingman ({REVIEW_TYPES})
- Verdict: {VERDICT}
- Findings addressed: {FIXED_COUNT} of {TOTAL_FIXABLE_COUNT} critical/warning findings fixed automatically
```

Rules for the summary:
- If the PR already has a well-written summary, preserve its content and augment it with the Review section. Do not discard information the author wrote.
- If the PR body is empty or minimal, write the full summary from the commit history and diff context.
- Keep the summary factual and concise. Do not editorialize or praise the changes.
- Preserve the machine-managed links block exactly when it already exists:

  ```markdown
  ## Related
  <!-- wingman-links:start -->
  ...
  <!-- wingman-links:end -->
  ```

  If that block exists, keep it as the authoritative Related section and do not paraphrase or replace its issue-link lines.
- Never remove the machine-managed links block.
- The Review section is always appended, even if the verdict was PASS with zero findings.
- Always use `gh pr edit` with a heredoc or temp file — never pass the body inline as a quoted string.

### Step 3: Update the title if needed

If the PR title is generic or inaccurate (e.g., "WIP" or a branch name), update it to a concise conventional-commit-style title:

```bash
gh pr edit {PR_NUMBER} --title "{UPDATED_TITLE}"
```

Only update the title if it is clearly inadequate. If the existing title is reasonable, leave it alone.

</step>

</process>

<success_criteria>
- [ ] PR identifier resolved and PR context gathered
- [ ] All requested reviewer types dispatched (all four, or specified single type)
- [ ] Findings consolidated and deduplicated across reviewers
- [ ] Review findings posted as a PR comment
- [ ] If verdict is PASS: review complete
- [ ] If verdict is NEEDS FIXES or MAJOR REVISION: fixable issues addressed in isolated worktree
- [ ] Fix changes committed and pushed to PR branch
- [ ] PR description updated with Review section reflecting final state
</success_criteria>

<error_handling>

## Error Handling

- **PR not found**: Stop immediately. Report the error with the identifier that was used.
- **Reviewer subagent timeout**: Record zero findings for that review type. Include a note in the posted comment: `[{REVIEW_TYPE} review timed out]`.
- **Missing prompt files**: If a review-type prompt file does not exist, skip that reviewer entirely. Log which reviewer was skipped.
- **Project rules**: Project rules are injected automatically into subagent context via `.claude/rules/` and the PreToolUse hook. The skill does not need to load or inject rules manually.
- **Fix subagent failure**: If a fix subagent crashes or returns malformed output, record it as a skipped fix with reason "subagent error" and continue with other fixes.
- **Git push failure**: Report the error. Do not force-push. Suggest the user resolve the conflict manually.

</error_handling>
