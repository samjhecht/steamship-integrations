---
name: Git & Version Control Standards
description:
category: git
---

# Git and Version Control Standards

## Conventional Commits

Format:

```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

### Required Types

- `feat`: New feature (MINOR in SemVer)
- `fix`: Bug fix (PATCH in SemVer)

### Common Additional Types

`build`, `chore`, `ci`, `docs`, `style`, `refactor`, `perf`, `test`, `revert`

### Breaking Changes

Append `!` after type/scope or include `BREAKING CHANGE:` footer (triggers MAJOR in SemVer).

### Examples

- `fix(auth): prevent race condition in token refresh`
- `feat(api)!: change response format for /users endpoint`
- `docs: correct installation instructions in README`
- `refactor(db): extract query builder into separate module`

## Branch Naming

Format: `<type>/<ticket-number>-<short-description>`

Prefixes: `feature/`, `bugfix/`, `hotfix/`, `release/`, `chore/`, `docs/`

Rules:

- Lowercase letters, numbers, hyphens only.
- Include ticket/issue numbers when available.
- Keep descriptions concise but descriptive.
- Examples: `feature/PROJ-123-user-authentication`, `bugfix/456-fix-null-pointer-in-search`

## Pull Request Standards

Based on Google Engineering Practices:

- **Core rule:** Approve a PR once it definitely improves overall code health, even if it is not perfect.
- **PRs should do one thing.** If a PR does multiple unrelated things, split it.
- **Keep PRs small.** Beyond ~400 lines of change, defect detection drops significantly.
- **Technical facts and data override personal opinions.** Style guides are the authority on style matters.
- **Use "Nit:" prefix** for non-blocking suggestions.
- **Never leave PRs stalled** over unresolved disagreements. Escalate through consensus, discussion, then technical leads.

## .gitignore

### Always Ignore

- Build artifacts: `dist/`, `build/`, `*.o`, `*.pyc`, `__pycache__/`
- Dependency directories: `node_modules/`, `vendor/`, `.venv/`
- IDE/editor files: `.idea/`, `.vscode/settings.json`, `*.swp`
- OS files: `.DS_Store`, `Thumbs.db`
- Environment/secret files: `.env`, `.env.local`, `credentials.json`, `*.pem`, `*.key`
- Log files: `*.log`, `logs/`
- Coverage/test output: `coverage/`, `.nyc_output/`, `htmlcov/`

### Rules

- Use GitHub's gitignore templates as a starting point.
- Use a global gitignore (`~/.gitignore_global`) for personal editor/OS files rather than polluting project-level files.
- Review periodically to ensure patterns are current.

## Commit Hygiene

- Each commit should represent a single logical change.
- Write commit messages that explain WHY, not just WHAT.
- Never commit commented-out code. Version control is your history.
- Never commit generated files that can be reproduced from source.
- Never commit secrets, credentials, or sensitive data.
