---
name: implementing-issue
description: Implements a single issue with TDD, code review, and fix loop. Use when a specific issue needs implementation -- either standalone or as part of orchestrated spec execution.
---

# Implementing Issue

<purpose>
Implements a single MCP issue with TDD and automated code review. Designed for two use cases:

1. **Standalone** -- user invokes directly for a specific issue
2. **Dispatched** -- called as the unit of work from implementing-specs

**Entry point:** `/wingman:implementing-issue {ISSUE_ID}`

**When to Use:**
- Implementing a specific issue (by ID)
- Invoked directly for standalone work
- Dispatched as a subagent from implementing-specs

**When NOT to Use:**
- Implementing a full specification (use implementing-specs)
- Exploring code (use locating-code)
- Answering questions (just answer directly)

**Input:**
- **Issue ID** (required): e.g., `42` (GitHub provider) or `ISS-000042` (markdown provider)
- **Working directory context** (optional): defaults to current directory
</purpose>

<required_context>

## Prerequisites

- Issue ID provided (GitHub issue number or markdown provider ID)
- MCP tools available: `issues_get` (with `include_body: true`), `issues_update`, `issues_mark_complete`
- Git repository with clean working tree (or worktree provided by orchestrator)
- Test runner configured for the project (e.g., `pnpm test`, `npm test`)
- Skills available: `practicing-tdd`, `requesting-code-review`, `verifying-before-completion`

</required_context>

<process>

<step name="setup" priority="first">

## 1. Setup

1. Read issue via `issues_get` MCP tool (use `include_body: true` to get the full description)
2. Capture working directory and branch (see `references/working-directory-protocol.md`)
3. Understand requirements and acceptance criteria from the issue description

</step>

<step name="implementation">

## 2. Implementation (TDD)

Follow the `practicing-tdd` skill strictly:

1. **RED** -- Write a failing test that captures the requirement
2. **GREEN** -- Implement the minimum code to make the test pass
3. **REFACTOR** -- Clean up implementation and tests
4. Commit work with a descriptive message

Repeat for each distinct requirement in the issue.

### Test Authenticity Rule

Tests MUST import and exercise the actual production code path. Never copy production logic into a test file and test the copy. A test that duplicates the implementation it is verifying provides zero regression protection -- when the production code changes, the copied test continues to pass against stale logic.

### Autonomy Boundaries

#### Auto-Fix Tiers

| Tier | Permission | Scope | Examples |
|------|-----------|-------|----------|
| 1 | Auto-fix, no ask | Bug fixes in this issue's own code | Typos, off-by-one, missing null checks |
| 2 | Auto-fix, document | Implied functionality not explicitly listed | Error handling, input validation, missing edge cases |
| 3 | Auto-fix, document | Blockers in adjacent code | Missing exports, incorrect types, broken imports |
| 4 | Escalate | Beyond this issue's scope | Architectural changes, shared interface modifications, scope expansion |

For Tier 2-3 fixes, document what was done and why in your completion report.

#### Documenting Deviations

When you make a reasonable simplification that deviates from the spec -- for example, implementing a simpler algorithm, skipping a non-essential feature, or choosing a different approach than specified -- document it explicitly so the verification phase can account for it:

- **In commit messages**: Include `DEVIATION: <rationale>` on its own line explaining what was simplified and why.
- **In code comments**: Add a comment near the relevant code with the format `// DEVIATION: <rationale>` (or the language-appropriate comment syntax).

This is for intentional, reasoned trade-offs -- not for incomplete work or skipped requirements. The downstream verify-integration skill uses these markers to distinguish deliberate simplifications from gaps.

#### Efficiency Guards

- **Analysis paralysis:** If you perform 5 consecutive read/search operations without writing code or making progress, STOP and report what is blocking you with specific details
- **Retry limit:** Do not retry the same failing approach more than 3 times -- escalate with the failure details and what you tried

</step>

<step name="code-review">

## 3. Code Review

1. Request code review using `requesting-code-review` skill
2. Parse feedback into severity categories:
   - **Critical** -- Must fix. Auto-fix with up to 2 attempts, escalate if both fail.
   - **Important** -- Must fix. Same 2-attempt process as Critical.
   - **Minor** -- Document only, do not fix.
3. Verify all Critical/Important issues resolved before proceeding

See `references/code-review-automation.md` for the detailed process.

</step>

<step name="completion">

## 4. Completion

1. Verify all tests pass
2. Provide TDD Compliance Certification (from practicing-tdd)
3. Verify git status (working tree clean, changes committed)
4. Report results:
   - Implementation summary (what was done)
   - Test results (pass count, coverage if available)
   - TDD Compliance Certification table
   - Commit hash
   - Any Minor issues noted for future cleanup

See `references/verification-checklist.md` for the full checklist.

</step>

</process>

<error_handling>

## Blocker Detection

Only stop for genuine blockers. See `references/blocker-detection.md` for the full decision flowchart.

**Immediate escalation:**
- Unclear requirements (do not guess)
- Git conflicts (do not auto-resolve)

**Escalation after 2 attempts:**
- Persistent test failures
- Fix subagent cannot resolve the issue
- Missing dependencies that cannot be auto-installed

**Non-blockers (continue autonomously):**
- First test failure -- auto-fix
- Code review feedback -- auto-fix (2 attempts)
- Warnings -- document and continue

</error_handling>

## Anti-Patterns

- Stopping to ask "should I continue?" when not blocked
- Guessing about unclear requirements instead of escalating
- Proceeding with failing tests
- Skipping code review
- Fixing Minor issues instead of documenting them

<success_criteria>
- [ ] Issue requirements fully read and understood
- [ ] Tests written first (TDD: RED phase confirmed)
- [ ] Implementation makes tests pass (GREEN phase)
- [ ] Code refactored as needed (REFACTOR phase)
- [ ] `verifying-before-completion` skill run — all checks pass
- [ ] Work committed with clear conventional commit message
- [ ] PR or review requested if required by project workflow
</success_criteria>

## Integration with Other Skills

- `practicing-tdd` -- TDD workflow (RED-GREEN-REFACTOR)
- `verifying-before-completion` -- Final verification checks
- `requesting-code-review` -- Code review dispatch and feedback parsing

## References

Detailed documentation in `references/` subdirectory:

- `working-directory-protocol.md` -- Location verification and command patterns
- `subagent-prompts.md` -- Subagent prompt templates (reviewer, fix agent)
- `code-review-automation.md` -- Review handling and fix loop process
- `verification-checklist.md` -- Completion verification steps
- `blocker-detection.md` -- Decision flowchart and escalation criteria
- `examples.md` -- Workflow examples

For workflow checklists, see `assets/workflow-checklist.md`.
