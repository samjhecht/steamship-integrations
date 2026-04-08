---
name: Dev/Prod Parity
description: Keep development, staging, and production environments as similar as possible
category: infrastructure
---

# Dev/Prod Parity

- Keep development, staging, and production environments as similar as possible in tooling, backing services, and runtime behavior
- Use the same type of backing service across all environments -- never substitute (e.g., SQLite in dev / Postgres in prod, or in-memory cache in dev / Redis in prod)
- Minimize the three gaps: time (deploy frequently), personnel (developers who write code should be close to deployment), tools (same stack everywhere)
