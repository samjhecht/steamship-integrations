---
name: Docs-as-Code
description: Treat documentation like source code -- version-controlled, reviewed, tested, and co-located
category: documentation
---

# Docs-as-Code

Treat documentation as a first-class artifact with the same lifecycle as source code: version-controlled, peer-reviewed, tested, and continuously deployed.

## Core Principles

- Documentation lives in the same repository as the code it describes.
- Documentation changes go through the same review process as code changes (pull requests, approvals).
- Documentation is written in plain-text markup (Markdown, AsciiDoc, reStructuredText) -- not in wikis, Google Docs, or Confluence.
- Documentation is built and deployed automatically via CI/CD.
- Every document has an owner. Unowned documentation rots.

## Workflow

- When a PR changes behavior, it must include corresponding documentation updates. Docs are part of the definition of done.
- Documentation PRs are reviewed for accuracy, clarity, and completeness -- not just rubber-stamped.
- Stale documentation is treated as a bug. File issues for it, prioritize fixing it.
- Use linting tools (markdownlint, vale, textlint) to enforce style consistency.

## Co-location

- API documentation lives next to the API code, not in a separate docs repo.
- Module or package documentation lives in the module directory (README.md, doc comments).
- Architecture and design documentation lives in `docs/` at the repo root.
- Do not scatter documentation across external systems (wikis, Notion, Confluence) unless there is a clear reason the audience cannot access the repo.

## Auto-generation

- API reference documentation should be generated from source code annotations (JSDoc, docstrings, OpenAPI specs).
- Configuration reference should be generated from schemas or validation code.
- CLI documentation should be generated from help text or argument parsers.
- Generated documentation must be clearly marked as generated and not hand-edited.

## Ownership and Maintenance

- Assign documentation owners the same way you assign code owners (CODEOWNERS file or equivalent).
- Review documentation coverage periodically. Unviewed or unupdated documents are candidates for deletion or rewriting.
- Prefer deleting outdated documentation over leaving it in place. Wrong documentation is worse than no documentation.

## Anti-patterns

- Wiki documentation with no ownership -- leads to 90%+ stale content (Google's GooWiki experience).
- Documentation in a separate repo that drifts from the code it describes.
- "Write the docs later" as a recurring pattern -- documentation written post-hoc is rarely written at all.
- Documentation that duplicates code comments verbatim instead of providing higher-level context.
