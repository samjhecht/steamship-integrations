---
name: Structured Logging
description: Core principles for structured logging -- event naming, log levels, JSON output, key-value context
category: logging
---

# Structured Logging

## Philosophy

- **Key-value pairs, not prose.** Enables machine parsing, filtering, and aggregation.
- **JSON to stdout.** 12-Factor App (Factor XI). No log files. Let infrastructure handle routing and retention.
- **Structured event names.** Use `snake_case` verb phrases as the event identifier. Context goes in separate fields.

## Log Levels

Use these levels consistently. When in doubt, prefer INFO over DEBUG.

| Level        | When to use                                   | Examples                                                        |
| ------------ | --------------------------------------------- | --------------------------------------------------------------- |
| **DEBUG**    | Dev-only diagnostics, internal state          | Variable values, SQL queries, re-render counts                  |
| **INFO**     | User-initiated actions, successful operations | File uploaded, export completed, job finished                   |
| **WARNING**  | Recoverable issues, fallbacks, deprecations   | Retry succeeded, fallback path used, deprecated endpoint called |
| **ERROR**    | Operation failures the user may notice        | API 500, processing failure, timeout                            |
| **CRITICAL** | System-level failures                         | Database unreachable, disk full, missing required config        |

- DEBUG must be OFF in production (security control for PII apps, not just noise reduction).
- If everything is INFO, nothing is. Reserve INFO for events worth recording in production.
- Log at ERROR when an operation fails; WARNING only when the system recovered automatically.

## Event Naming Conventions

- `{noun}_{verb_past}` for completed events: `document_created`, `export_completed`
- `{noun}_{verb}_started` for operation start: `detection_started`, `upload_started`
- `{noun}_{verb}_failed` for failures: `conversion_failed`, `auth_failed`

## Cross-Cutting Rules

- Performance-critical operations log start + completion with `duration_ms`.
- Never catch and silence errors -- at minimum, log before re-raising or handling.
- All timestamps in UTC (ISO 8601).
