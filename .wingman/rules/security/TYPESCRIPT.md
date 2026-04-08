---
name: Typescript Security Principles
description: Typescript-specific security standards
category: security
paths:
  - "**/*.ts"
  - "**/*.tsx"
  - "**/*.mts"
  - "**/*.cts"
---

# TypeScript Security Standards

## Input Validation

- Validate all external input at API boundaries with a schema validator (Zod recommended).
- Define schemas at the boundary. Infer TypeScript types from schemas: `z.infer<typeof schema>`.
- Validate request bodies, query params, path params, and headers.
- Client-side validation is UX only. Server-side validation is authoritative.
- Use allowlist validation, not blocklist. Define what IS allowed rather than what is NOT.

### Environment Variable Validation

Validate `process.env` at application startup with a schema. Fail fast if required variables are missing:

```typescript
const envSchema = z.object({
  DATABASE_URL: z.string().url(),
  API_KEY: z.string().min(1),
  PORT: z.coerce.number().default(3000),
});
export const env = envSchema.parse(process.env);
```

## Injection Prevention

- Never use `eval()`, `Function()`, or `child_process.exec()` with unsanitized input.
- Use parameterized queries for all database operations. Never concatenate user input into SQL.
- Escape HTML/JavaScript content in output. Never output raw user data in responses.
- Test regular expressions for ReDoS vulnerabilities.

## Dependency Security

- Run `npm audit` in CI. Block deploys on critical/high vulnerabilities.
- Use `npm ci` in CI, never `npm install`. Lockfile integrity prevents supply chain drift.
- Commit lockfiles (`package-lock.json` / `pnpm-lock.yaml`) to version control.
- Disable lifecycle scripts by default. Use explicit allow-lists when scripts are genuinely needed.
- Pin dependency versions or use narrow ranges.
- Review every dependency update. Do not auto-merge major or minor bumps without review.
- Minimize your dependency tree. Fewer dependencies = smaller attack surface.

## Secrets Management

- Never commit secrets to source control.
- Use `.env` files for local development only. Add `.env` to `.gitignore`.
- Maintain a `.env.example` with required variable names but no values.
- In production, use a secrets manager (AWS Secrets Manager, HashiCorp Vault, Infisical), not environment variables directly.
- Never log secrets. Configure logging libraries to redact sensitive fields.

## Authentication and Authorization

- Hash passwords with bcrypt or argon2. Never store plaintext.
- Implement rate limiting on authentication endpoints.
- Set cookie flags: `httpOnly: true`, `secure: true`, `sameSite: "strict"`.
- Implement CSRF protection for state-changing requests.
- Apply the principle of least privilege for all roles and service accounts.

## HTTP Security Headers

Use `helmet` middleware or equivalent. At minimum:

- `Strict-Transport-Security` (HSTS)
- `X-Frame-Options: DENY`
- `Content-Security-Policy` (restrict script sources)
- `X-Content-Type-Options: nosniff`
- Remove `X-Powered-By` header.
- Disable caching on sensitive responses.

## Error Handling

- Never expose stack traces to end users.
- Bind `uncaughtException` and `unhandledRejection` handlers. Log and exit gracefully (do not attempt to resume).
- Set request body size limits per content type.
- Monitor the event loop. Return 503 when the server is overloaded.

## Code Review: Security Checks

- All external input validated with a schema validator before use.
- No secrets or credentials in code or config files.
- No `eval()`, `Function()`, or `child_process.exec()` with dynamic input.
- Dependencies reviewed for known vulnerabilities.
- Error messages do not leak internal details to clients.
