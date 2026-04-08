---
name: PII Safety in Logs
description: Rules for preventing PII and sensitive data from leaking into log output
category: logging
---

# PII Safety in Logs

## Defense-in-Depth Strategy

1. **Developer discipline (primary):** Never pass sensitive data to the logger.
2. **Safety-net redaction:** A log processor that pattern-matches common PII formats (SSN, email, phone) in log output. Catches mistakes -- not a substitute for layer 1.
3. **Infrastructure pipeline rules:** Log aggregation pipelines should have their own redaction rules as a final backstop.

## What to Log vs. What Not to Log

| Safe to log                            | Never log                                  |
| -------------------------------------- | ------------------------------------------ |
| Request IDs, correlation IDs, task IDs | Document text content                      |
| Entity counts, entity types            | Actual PII strings (names, SSNs, emails)   |
| Operation names and durations          | User selections or highlighted text        |
| File names, file sizes, MIME types     | File contents or preview data              |
| Status codes, error codes              | Authentication tokens, passwords, API keys |
| User IDs (opaque identifiers)          | Session tokens, cookies                    |
