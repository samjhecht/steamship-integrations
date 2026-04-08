---
name: Docker Security
description:
category: security
paths:
  - "**/Dockerfile"
  - "**/Dockerfile.*"
  - "**/docker-compose*.yml"
  - "**/docker-compose*.yaml"
  - "**/.dockerignore"
---

## Docker Security

- Use specific base image tags: `node:20.11-alpine` not `node:latest`.
- Use minimal base images (Alpine, distroless, slim) to reduce attack surface.
- Run as non-root user: `USER appuser`.
- Use multi-stage builds to separate build dependencies from runtime.
- Use `.dockerignore` to exclude `node_modules/`, `.git/`, test files from build context.
- Scan images for vulnerabilities before pushing to registries.
- Never use `--privileged` mode in production.
