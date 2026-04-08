---
name: Python Security Standards
description: Language-specific security standards for Python projects
category: security
paths:
  - "**/*.py"
---

# Python Security Standards

## Dangerous Functions -- Never Use on Untrusted Input

| Function | Danger | Safe Alternative |
|----------|--------|-----------------|
| `eval()` | Executes arbitrary Python code | `ast.literal_eval()` for literal parsing, or explicit parsing |
| `exec()` | Executes arbitrary statements | Redesign to avoid dynamic code execution |
| `pickle.loads()` / `pickle.load()` | Arbitrary code execution via `__reduce__` | Use JSON, MessagePack, or Protocol Buffers |
| `yaml.load()` without SafeLoader | Code execution via `!python/object` tags | Always use `yaml.safe_load()` |
| `os.system()`, `subprocess.call(shell=True)` | Shell injection | `subprocess.run()` with `shell=False`, args as list |
| `marshal.loads()` | Can execute code | Use JSON for data exchange |
| `shelve.open()` | Uses pickle internally | Use JSON/SQLite for persistent storage |
| `__import__()` with user input | Arbitrary module loading | Explicit imports or allowlist of modules |

## Input Validation

- Validate all external input at the boundary using Pydantic models (for APIs) or explicit validation functions.
- Use allowlists, not blocklists, for input validation.
- Canonicalize input data before validating (normalize Unicode, resolve paths, decode encodings).
- Enforce consistent encoding (UTF-8) across all string operations.
- Use Pydantic's `constr(regex=...)`, `conint(ge=0)`, `EmailStr` for typed validation.
- Validate file uploads: check MIME types, enforce size limits, never trust filenames.
- Never pass user input directly to format strings with untrusted keys: `template.format(**user_dict)` is dangerous.

## SQL Injection Prevention

- Always use parameterized queries: `cursor.execute("SELECT * FROM users WHERE id = %s", (user_id,))`.
- Never concatenate user input into SQL strings.
- In SQLAlchemy, use `text()` with named parameters: `session.execute(text("SELECT * FROM users WHERE id = :id"), {"id": user_id})`.
- Prefer ORM query methods over raw SQL. ORMs parameterize by default.
- Create dedicated database users with minimum required permissions.

## Authentication and Password Handling

- Never store plaintext passwords. Always hash with bcrypt (rounds >= 12) or argon2id.
- Use `passlib` with `CryptContext(schemes=["bcrypt"])` or the `bcrypt` library directly.
- JWT secret keys must be at least 32 random characters, stored in environment variables, never in source code.
- Set JWT token expiration to 15-30 minutes for access tokens.
- Implement rate limiting on authentication endpoints.
- Never store sensitive data in JWT payloads (signed but not encrypted).
- Log authentication failures for audit trails but never log passwords or tokens.

## Secrets Management

- Never hardcode credentials, API keys, tokens, or passwords in source code.
- Always add `.env` to `.gitignore`.
- Use environment variables or `pydantic-settings` (`BaseSettings`) to load configuration.
- In production, use a dedicated secrets manager (AWS Secrets Manager, GCP Secret Manager, HashiCorp Vault).
- Use `secrets` module for generating cryptographically secure tokens. Never use `random` for security purposes.
- Use pre-commit hooks or CI tools (TruffleHog, Gitleaks) to prevent accidental secret commits.

## Dependency Security

- Run `pip-audit` in CI to scan dependencies against known vulnerability databases.
- Run `bandit` as a static analysis tool in CI to catch security anti-patterns.
- Pin all dependency versions in `requirements.txt` or `pyproject.toml`.
- Never install packages from untrusted sources. Verify package names carefully (typosquatting defense).

## Cryptography

- Use `secrets` module (not `random`) for security-sensitive random generation.
- Never implement your own cryptographic algorithms. Use established libraries (`cryptography`, `PyNaCl`).
- Use `secrets.token_urlsafe(32)` for generating API keys and session tokens.

## Error Handling and Logging

- Never log sensitive data: passwords, tokens, API keys, PII.
- Sanitize all data written to logs to prevent log injection attacks.
- Never use bare `except:`. Always catch specific exception types.
- Never catch `Exception` or `BaseException` unless re-raising or at an isolation boundary.
- Minimize code in `try` blocks. Wrap only the statement that can raise.
- Never use `assert` for input validation. Assert statements can be stripped with `-O` flag.
- Return structured error responses from APIs, never raw exception tracebacks.

## Code Review: Security Checklist

- No use of `eval()`, `exec()`, `pickle.loads()`, `yaml.load()` without SafeLoader, `os.system()`, `subprocess` with `shell=True`.
- No hardcoded secrets in source code.
- All external input validated before use.
- All database queries use parameterized queries or ORM methods.
- No bare `except:` or overly broad exception catches.
- All files/connections opened with `with` statements.
- Passwords hashed with bcrypt/argon2, never stored as plaintext.
- CORS configured to allow only known origins.
- Dependencies pinned and scanned.
