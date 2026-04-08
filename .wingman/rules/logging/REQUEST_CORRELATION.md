---
name: Request Correlation
description: Standards for propagating request IDs across services for end-to-end tracing
category: logging
---

# Request Correlation

Every request gets a unique ID for end-to-end tracing.

## Implementation Layers

| Layer        | Mechanism                                                           |
| ------------ | ------------------------------------------------------------------- |
| **Backend**  | Middleware generates/reads `X-Request-ID`. Injected into log context |
| **Frontend** | API client attaches `X-Request-ID` header to every outbound request |
| **Logs**     | Correlation ID appears automatically in every log line              |

## Rules

- Every HTTP request must carry an `X-Request-ID` header.
- Backend middleware generates the ID if not present in the incoming request.
- The correlation ID must propagate to all downstream service calls.
- All log entries within a request lifecycle must include the correlation ID.
- Use established middleware libraries (e.g., `asgi-correlation-id` for Python/ASGI) rather than writing custom request ID propagation.
