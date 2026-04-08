---
name: running-workflows
description: Launches workflow engine runs in the background, monitors progress via session files, and reports status. Use when running construyelo workflows or any workflow engine invocation.
---

# Running Workflows

<purpose>
Standardized way to launch the workflow engine, background it, and monitor progress. Keeps the main conversation available while workflows run.

**When to Use:**
- User asks to "run a workflow", "implement a spec", or "execute construyelo"
- You need to invoke the workflow engine
- Monitoring a running or paused workflow

**When NOT to Use:**
- Implementing a single issue (use implementing-issue)
- Writing plans without execution (use writing-plans)
</purpose>

<required_context>
## Discovering Available Workflows

Use `workflow_list` to see available workflows and their descriptions. Use `workflow_get({ name: 'workflow-name' })` to inspect a workflow's phases and configuration before running it.
</required_context>

<process>

<step name="launch" priority="first">
## Launching a Workflow

### Via MCP Tool (Preferred)

Use the `workflow_run` MCP tool. It launches the workflow engine in-process as a background async task -- no child process spawning or CLI path resolution needed.

```
workflow_run({
  workflow: "construyelo",
  input: { spec_reference: "#42" }
})
```

**Parameters:**

- `workflow` (required) -- Workflow file name (e.g., `"construyelo"` or `"construyelo.yaml"`)
- `input` (optional) -- Input variables as key-value pairs (e.g., `{ "spec_reference": "#42" }`)
- `spec` (optional) -- Path to specification file
- `dryRun` (optional) -- Run plan phase only (boolean)
- `resume` (optional) -- Session ID to resume from checkpoint
- `model` (optional) -- Model override (default: opus)

The tool returns immediately with the workflow name and working directory. Use `session_status({})` to monitor progress.

### Via CLI (Advanced / Development)

If running from the wingman monorepo or when the MCP tool is unavailable:

```bash
node packages/agent-core/dist/integration/cli.js run <workflow>.yaml [options]
```

Or via the plugin's bin wrapper:

```bash
bin/wingman-workflow run <workflow>.yaml [options]
```

**CLI Options:**

- `--spec <file>` -- Path to specification file
- `--input <key=value>` -- Set input variable (repeatable)
- `--dry-run` -- Analyze and plan only
- `--resume <session-id>` -- Resume from checkpoint
- `--working-dir <dir>` -- Override working directory
- `--model <model>` -- Override default model
- `--workflow-dir <dir>` -- Override workflow base directory

### MUST Background by Default

Workflows take 5-30 minutes. When using the CLI directly (not the MCP tool), always launch in the background using the Bash tool's `run_in_background` parameter.

After launching, immediately tell the user:

- The workflow is running in the background
- The session ID (from initial output)
- How to check status: "Ask me anytime for a progress update"
</step>

<step name="notifications">
## Automatic Completion Notifications

The workflow engine automatically delivers status notifications when a workflow completes, fails, or pauses. You do not need to poll for completion.

**How it works:**

- A `SessionNotificationReporter` runs alongside every workflow (always-on).
- When the workflow reaches a terminal state, a notification is written to `.wingman/sessions/{sessionId}/notifications/`.
- A PostToolUse hook (`workflow-notify.cjs`) checks for pending notifications after each tool call and injects them into your context via `additionalContext`.
- A Stop hook (`stop-guard.cjs`) prevents you from going idle when a notification is pending -- it blocks the stop and delivers the notification content.

**What the notification contains:**

- Workflow name and session ID
- Terminal status (completed / failed / paused)
- Duration, branch name, PR URL (if created)
- Task progress (completed/total), cost summary
- Error details (for failures) or blocker description (for pauses)

**What you should do when you receive a notification:**

- Report the results to the user immediately
- For completions: mention the branch, PR URL, and task count
- For failures: include the error message and suggest debugging steps
- For pauses: include the blocker description and offer to resume

**Fallback:** If notifications are not delivered (e.g., hooks not configured), use `session_status({})` to check manually.
</step>

<step name="check-progress">
## Checking Progress

### MCP Tool (Preferred)

Use the `session_status` MCP tool for a structured status report:

```
session_status({ sessionId: "wf-2026-02-16-ba6fcb97" })
```

If you omit `sessionId`, it auto-detects the most recent `wf-*` session:

```
session_status({})
```

The tool returns structured metadata including:

- `status`: running/paused/completed/failed
- `activeStep`: Current step derived from audit log (reliable, not stale context.json)
- `phasesCompleted`: Phases finished (derived from audit log)
- `tasksCompleted` / `tasksPending` / `totalTasks`: Task progress
- `duration`: Human-readable elapsed time
- `specFile`, `worktreePath`, `branchName`: Session context
- `blocker`: Details if the workflow is paused
- `checkpoint`: Resume state if available
- `lastActivity`: What happened most recently
- `auditEntryCount`: Total audit entries

### Manual Status Check

If neither tool is available, check session files directly:

**Session directory:** `.wingman/sessions/<session-id>/`

#### audit.jsonl -- Activity Log (Source of Truth)

The audit log is the most reliable source of current workflow state. Each line is a JSON object recording a step transition:

```json
{"step":"plan","status":"started","timestamp":"2026-02-16T21:58:15.127Z"}
{"step":"plan","status":"completed","timestamp":"2026-02-16T21:59:28.160Z","metadata":{...}}
{"step":"implement","status":"started","timestamp":"2026-02-16T22:03:48.990Z"}
```

Steps you will see: `init`, `plan`, `review`, `implement` (per-task), `fix-issues` (if review found problems), `review-pr`.

#### context.json -- Overall State

Contains `status`, `tasksCompleted`, `tasksPending`, `specFile`, `worktreePath`, `branchName`. Note: `currentPhase` and `phasesCompleted` may be stale -- derive these from audit.jsonl instead.

#### checkpoint.json / blocker.json

Present when the workflow paused. Checkpoint contains resume state; blocker contains the reason for pausing.

### Interpreting Progress

| Status      | Meaning                          | Action                                 |
| ----------- | -------------------------------- | -------------------------------------- |
| `running`   | Workflow is actively executing   | Wait, check audit log for current step |
| `paused`    | Hit a blocker or loop exhaustion | Read blocker, consider resuming        |
| `completed` | All phases finished successfully | Check worktree for PR                  |
| `failed`    | Unrecoverable error              | Read audit log for error details       |

### Reporting to User

When the user asks for a progress update, call `session_status({})` and report:

1. **Status**: running/paused/completed/failed
2. **Active step**: What's happening right now
3. **Progress**: X of Y tasks completed (if in implement phase)
4. **Last activity**: Most recent audit entry
5. **Duration**: How long it has been running

Example report:

```
Workflow wf-2026-02-16-ba6fcb97 is running.

Phase: implement (plan -> review -> [implement] -> review-pr)
Progress: 3/8 tasks completed
Last activity: Implementing task-004 (started 2m ago)
Running for: 12 minutes

The workflow is working in an isolated worktree at .worktrees/feature-name/.
```
</step>

<step name="handle-completion">
## Handling Completion

When the workflow finishes, you will typically be notified automatically via the PostToolUse or Stop hooks (see "Automatic Completion Notifications" above). If you need to check manually:

**If completed:**

- Report success to user
- Show the branch name and worktree path
- Note if a PR was created
- Suggest reviewing the changes

**If paused:**

- Read blocker.json for the reason
- Report to user with context
- Offer to resume: `workflow_run({ workflow: "<name>", resume: "<session-id>" })`

**If failed:**

- Read the last audit entries for error context
- Report the failure to user
- Suggest debugging steps
</step>

<step name="resume">
## Resuming Paused Workflows

```
workflow_run({ workflow: "<workflow-name>", resume: "<session-id>" })
```

The engine picks up from the last checkpoint, skipping completed phases and tasks.
</step>

</process>

<success_criteria>
- [ ] Workflow launched successfully (no startup errors)
- [ ] Session ID captured and reported to user
- [ ] User informed of how to check workflow status
- [ ] Completion notification received and reported when workflow finishes
- [ ] Failure or pause conditions reported with actionable details
</success_criteria>

## Workflow Phases Reference

The `construyelo` workflow runs these phases in order:

1. **plan** -- Creates MCP issues from the spec
2. **review** -- Validates completeness and feasibility
3. **implement** -- Implements each task in a worktree with TDD
4. **review-pr** -- Dispatches parallel reviewers, posts findings, auto-fixes

Each phase produces audit entries and updates context.json.
