---
name: Config Separation
description: Configuration that varies between deploys must live outside code
extends: SECURITY.md
category: infrastructure
---

# Config Separation

- Configuration that varies between deploys (credentials, backing service URLs, per-environment flags) MUST live outside code -- in environment variables or a secrets manager, never in committed files
- Litmus test: could the codebase be open-sourced right now without leaking any credentials?
- Internal application config that is constant across all deploys (route definitions, DI wiring, framework settings) belongs in code, not env vars
- Do not group config into named environment bundles ("development", "staging") -- manage each variable independently per deploy
