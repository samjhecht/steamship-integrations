---
name: Documentation Standards
description: General documentation standards for code comments, READMEs, and API docs
category: documentation
---

# Documentation Standards

## Code Comments: When and When Not

### When to Comment

- **WHY** something is done a non-obvious way (business rules, workarounds, constraints).
- **WARNING** about non-obvious consequences or gotchas.
- **TODO/FIXME** with ticket numbers for known technical debt.
- Complex algorithms that cannot be made self-documenting.
- Public API contracts (parameters, return values, exceptions).

### When NOT to Comment

- Restating what the code already says (`i += 1  // increment i`).
- Explaining bad naming (fix the name instead).
- Commented-out code (delete it; version control has history).
- Changelog-style comments at the top of files.

## README Standards

Essential sections for every project README:

1. **Title and description:** One paragraph explaining what the project does.
2. **Installation:** How to set it up locally.
3. **Usage:** Basic usage examples.
4. **Development:** How to build, test, and contribute.
5. **License:** What license applies.

Additional sections as relevant:

- Prerequisites/requirements
- Configuration
- API reference (or link to it)
- Contributing guidelines

Rules:

- Keep descriptions concise. Use code blocks for commands.
- Update when the project changes.
- Limit badges to 2-4 (build status, version, license, coverage).

## API Documentation

- Use OpenAPI/Swagger for REST APIs. Generate from code annotations where possible.
- Include for every endpoint: method, path, description, parameters, request body schema, response schemas, authentication requirements, example request/response pairs.
- Keep examples realistic. Use plausible data, not "foo" and "bar."
- Document error responses as thoroughly as success responses.
- Version documentation alongside the API.
