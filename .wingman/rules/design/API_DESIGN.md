---
name: API Design Standards
description:
category: design
---

# API Design Standards

## REST Conventions

- Use nouns for resources, not verbs: `/users` not `/getUsers`.
- Use plural nouns: `/articles` not `/article`.
- HTTP methods carry the verb: GET (read), POST (create), PUT (full update), PATCH (partial update), DELETE (remove).
- Use kebab-case in URLs: `/user-profiles` not `/userProfiles`.
- Version your API: `/api/v1/resources`.

## HTTP Status Codes

Use appropriate status codes consistently:

| Code | Meaning               | When to Use                                            |
| ---- | --------------------- | ------------------------------------------------------ |
| 200  | OK                    | Successful GET, PUT, PATCH, or DELETE                  |
| 201  | Created               | Successful POST that creates a resource                |
| 204  | No Content            | Successful DELETE with no response body                |
| 400  | Bad Request           | Invalid request syntax or parameters                   |
| 401  | Unauthorized          | Missing or invalid authentication                      |
| 403  | Forbidden             | Authenticated but lacks permission                     |
| 404  | Not Found             | Resource does not exist                                |
| 409  | Conflict              | Request conflicts with current state (e.g., duplicate) |
| 422  | Unprocessable Entity  | Valid syntax but semantically invalid                  |
| 429  | Too Many Requests     | Rate limit exceeded (include `Retry-After` header)     |
| 500  | Internal Server Error | Unexpected server failure                              |

## Error Response Format

Use a consistent error response structure across all endpoints:

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Human-readable description",
    "details": [
      {
        "field": "email",
        "message": "Must be a valid email address"
      }
    ]
  }
}
```

- Error codes should be machine-readable constants (UPPER_SNAKE_CASE).
- Messages should be human-readable and safe to display to end users.
- Details array provides field-level validation errors when applicable.
- Never expose stack traces or internal implementation details in error responses.

## Pagination

- Implement pagination for all collection endpoints.
- Prefer cursor-based pagination over offset for large datasets (avoids drift on concurrent writes).
- Return pagination metadata in the response:
  ```json
  {
    "data": [...],
    "pagination": {
      "cursor": "abc123",
      "hasMore": true,
      "totalCount": 1234
    }
  }
  ```

## Filtering, Sorting, and Field Selection

- Support filtering via query parameters: `GET /users?status=active&role=admin`.
- Support sorting: `GET /users?sort=created_at&order=desc`.
- Support field selection to reduce payload size: `GET /users?fields=id,name,email`.

## Rate Limiting

- Implement rate limiting on all public endpoints.
- Return `429 Too Many Requests` with `Retry-After` header when limits are exceeded.
- Include rate limit headers in responses: `X-RateLimit-Limit`, `X-RateLimit-Remaining`, `X-RateLimit-Reset`.

## General API Rules

- APIs must be idempotent where HTTP semantics require it (GET, PUT, DELETE).
- POST is not idempotent by default. Use idempotency keys for critical operations (payments, order creation).
- Return the created/updated resource in POST/PUT/PATCH responses.
- Use `Content-Type: application/json` for all JSON APIs.
- Validate all input at the API boundary before passing to business logic.
- Document all endpoints with OpenAPI/Swagger.
