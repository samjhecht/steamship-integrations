---
name: Error Handling Patterns
description: Shallow error hierarchies, error code design, operational vs programmer errors
category: logging
---

# Error Handling Patterns

## Philosophy

| Principle                            | Rationale                                                                                                                 |
| ------------------------------------ | ------------------------------------------------------------------------------------------------------------------------- |
| **Shallow error hierarchies**        | 3-4 exception classes + error `code` strings. Deep inheritance adds complexity without value                              |
| **Operational vs programmer errors** | Operational errors (network timeout, disk full) get retries and user messages. Programmer errors get stack traces and bugs |

## Error Hierarchy Design

Use a shallow hierarchy of 3-4 exception/error classes. Differentiate specific errors via a `code` string field, not subclasses.

Use the `code` field for specificity:

```
raise ProcessingError("Unsupported file type", code="UNSUPPORTED_FORMAT")
raise ProcessingError("Timeout during OCR", code="OCR_TIMEOUT")
```

## Error Class Contracts

Every error class should carry both:

- `message` -- technical detail for logs
- `userMessage` -- safe, helpful text for display in the UI

## Rules

- Never catch and silence errors. At minimum, log the error before handling.
- Catch specific exception types, not broad catch-all handlers.
- Separate user-facing messages from internal details. Log full stack traces; show users a generic message with a correlation ID.
