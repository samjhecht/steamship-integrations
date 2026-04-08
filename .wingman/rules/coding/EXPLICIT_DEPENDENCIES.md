---
name: Explicit Dependencies
description: Declare all dependencies in a manifest, never rely on implicit system-wide packages
category: coding
---

# Explicit Dependencies

- Declare ALL dependencies in a manifest (package.json, requirements.txt, pyproject.toml, go.mod) -- never rely on implicit system-wide packages
- Use dependency isolation (node_modules, virtualenv, bundler) so the app makes no assumptions about what's available on the host
- If the app requires system tools (ImageMagick, ffmpeg, curl), document them explicitly in setup instructions or bundle them in the container image
