---
name: CLI Interface Mandate
description: Boundary capabilities must be CLI-accessible with text/JSON I/O
category: design
---

#### CLI Interface Mandate

Every capability that accepts external input or produces external output must be accessible through a CLI. Internal utility functions consumed only by other code are exempt. Said CLI interfaces MUST:

- Accept text as input (via stdin, arguments, or files)
- Produce text as output (via stdout)
- Support JSON format for structured data exchange

This enforces observability and testability. The LLM cannot hide functionality inside opaque classes—everything must be accessible and verifiable through text-based interfaces.
