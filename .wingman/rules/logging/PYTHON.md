---
name: Python Logging Standards
description: Python-specific logging, error handling, and observability standards (structlog, FastAPI, RFC 9457)
category: logging
paths:
  - "**/*.py"
---

# Python Logging Standards

## Logger Acquisition

```python
from observability.logging_config import get_logger
log = get_logger(__name__)
```

Always use the project's `get_logger()`. Never `logging.getLogger()` directly or `print()`.

## Structured Messages

```python
# GOOD -- snake_case event, keyword context
log.info("document_upload_started", filename=file.filename, file_size_bytes=size)

# BAD -- string interpolation
log.info(f"Document {file.filename} uploaded ({size} bytes)")

# BAD -- nested dicts obscure fields from aggregators
log.info("upload", extra={"metadata": {"filename": file.filename}})
```

## Configuration Enums

Environment names and log levels must use typed enums, never bare strings:

```python
from enum import StrEnum

class Environment(StrEnum):
    DEVELOPMENT = "development"
    STAGING = "staging"
    PRODUCTION = "production"
```

## Logging Output by Environment

| Environment | Renderer                              | Format                    |
| ----------- | ------------------------------------- | ------------------------- |
| Development | `structlog.dev.ConsoleRenderer()`     | Colorized, human-readable |
| Production  | `structlog.processors.JSONRenderer()` | JSON lines to stdout      |

## OpenTelemetry Compatibility

structlog's `merge_contextvars` is compatible with OpenTelemetry's context propagation. Use semantic field names (`http.method`, `http.status_code`, `duration_ms`) where they align with OTel conventions.

## Error Hierarchy (Backend)

| Class                  | HTTP Status  | Use for                                                  |
| ---------------------- | ------------ | -------------------------------------------------------- |
| `AppError`             | 500          | Base class -- do not raise directly                      |
| `ValidationError`      | 400          | Bad input, invalid request data                          |
| `ProcessingError`      | 500 (varies) | All domain processing failures. Use `code` for specifics |
| `ExternalServiceError` | 503          | External dependency unavailable or timed out             |

## Error Response Format (RFC 9457)

All API error responses use [RFC 9457 Problem Details](https://www.rfc-editor.org/rfc/rfc9457):

```json
{
  "type": "https://docs.example.com/errors/unsupported-format",
  "title": "Unsupported Format",
  "status": 400,
  "detail": "File type '.xyz' is not supported.",
  "instance": "/api/documents/abc-123/convert",
  "code": "UNSUPPORTED_FORMAT",
  "request_id": "req-550e8400-e29b"
}
```

- Never return raw `HTTPException`. Use custom error classes so the global exception handler produces RFC 9457 responses.
- Set `Content-Type: application/problem+json`.
- Include `request_id` for correlation.
- Register handlers for `RequestValidationError` and `StarletteHTTPException` so all errors get the same shape.

## Anti-Patterns

| Anti-pattern                                | Fix                                                        |
| ------------------------------------------- | ---------------------------------------------------------- |
| `log.info(f"Uploaded {name}")`              | `log.info("file_uploaded", filename=name)`                 |
| `log.info("upload", extra={"meta": {...}})` | Flatten to top-level keyword args                          |
| `raise HTTPException(status_code=400, ...)` | `raise ValidationError("Bad input", code="INVALID_FIELD")` |
| `except Exception: pass`                    | Catch specific types, log before handling                  |
| `print("debug:", value)`                    | `log.debug("event_name", key=value)`                       |
| `if env == "production":`                   | `if env == Environment.PRODUCTION:`                        |
| `logging.getLogger(__name__)`               | `get_logger(__name__)`                                     |
