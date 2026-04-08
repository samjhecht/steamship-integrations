---
name: Language-Agnostic Security Fundamentals
description:
category: security
---

# Security Fundamentals (Cross-Language)

## Core Principles

- **Defense in depth:** Multiple layers of security. Never rely on a single control.
- **Least privilege:** Every component, user, and service account gets the minimum permissions required.
- **Fail securely:** When something fails, default to denying access, not granting it.
- **Trust boundaries:** Validate data every time it crosses a trust boundary (user input, API calls, database reads, file reads).

## Input Validation

- Validate all external input at system boundaries before processing.
- Use allowlists (define what IS allowed) over blocklists (define what is NOT).
- Validate type, length, range, format, and encoding.
- Canonicalize input before validation (normalize Unicode, resolve paths, decode encodings).
- Client-side validation is a UX convenience only. Server-side validation is authoritative.

## Secrets Management

- Never commit secrets to version control. Use `.env` for local dev, add to `.gitignore`.
- Maintain `.env.example` with required variable names but no values.
- In production, use a secrets manager (AWS Secrets Manager, HashiCorp Vault, GCP Secret Manager).
- Rotate secrets regularly and support rotation without deployment.
- Never log secrets. Configure logging to redact sensitive fields.
- Use pre-commit hooks or CI tools (Gitleaks, TruffleHog) to prevent accidental secret commits.

## Authentication

- Hash passwords with bcrypt (work factor >= 12) or argon2id. Never store plaintext.
- Rate limit authentication endpoints to prevent brute force.
- Use HTTPS in all environments handling real credentials.
- Set session/cookie flags: `httpOnly`, `secure`, `sameSite: strict`.
- JWT access tokens should expire in 15-30 minutes.
- Never store sensitive data in JWT payloads (signed but not encrypted).

## Dependency Security

- Pin dependency versions. Commit lockfiles to version control.
- Run vulnerability scanning in CI (`npm audit`, `pip-audit`, `cargo audit`, equivalent for your stack).
- Block deploys on critical/high severity vulnerabilities.
- Review dependency updates before merging. Do not auto-merge major/minor bumps.
- Minimize your dependency tree. Fewer dependencies = smaller attack surface.
- Disable lifecycle scripts by default where the package manager supports it.

## CI/CD Security

- Never store secrets in pipeline config files. Use the CI platform's secret management.
- Pin action/image versions to specific SHAs or tags, not `latest`.
- Run security scanning in CI: dependency scanning, SAST, container image scanning.
- Enforce branch protection: require PR reviews, passing CI, no force-pushes to main.
- CI service accounts get minimal required permissions.

## Error Handling

- Never expose stack traces, internal paths, or implementation details to end users.
- Log detailed errors internally with correlation IDs.
- Return generic error messages to clients with reference IDs for debugging.

## Logging Security

- Never log: passwords, tokens, API keys, credit card numbers, SSNs, PII.
- Sanitize log output to prevent log injection (newlines, control characters).
- Include correlation IDs in all log entries for distributed tracing.
- Implement log rotation and retention policies.
