---
name: writing-docs
description: Generates user-facing documentation from specifications with auto-detection of pre-implementation vs post-implementation context. Use when writing docs for a feature (conceptual overviews, READMEs, API reference, tutorials, how-to guides) either before or after code exists.
---

# Writing Docs

## Overview

This skill generates user-facing documentation from specifications. It operates in two modes -- **pre-implementation** (spec only) and **post-implementation** (spec + code) -- auto-detected based on whether implementation code exists. A single skill handles both because the process is the same; only the source material and tone differ.

**Pre-impl:** Docs describe what will be built. Serves as design validation -- if you cannot write clear docs, the design probably has problems.

**Post-impl:** Docs describe what was built. Must be verified against actual code for accuracy.

## When to Use

- Writing docs for a new feature (before or after code)
- Updating docs after implementation changes
- Docs-first design validation (write docs before code to test the design)
- Standalone invocation or as part of the implementing-specs workflow

## Phase Detection

Auto-detect the mode:

1. Read the specification to understand what is being built
2. Search for implementation code described in the spec (files, modules, functions, classes)
3. If implementation artifacts exist -> **post-implementation mode**
4. If no implementation artifacts found -> **pre-implementation mode**

The user can override auto-detection by stating the mode explicitly (e.g., "write pre-implementation docs" or "write post-implementation docs").

## Documentation Standards

Read `.wingman/rules/DOCUMENTATION.md` for all placement rules, structure requirements, and accuracy requirements before writing any docs.

## Pre-Implementation Mode

**Source material:** Specification only, plus existing codebase patterns for style consistency.

**What to produce:**

- Conceptual overview -- what the feature is, why it exists, how it fits into the system
- Architecture narrative -- key design decisions and component relationships
- README/getting-started draft -- how users will interact with the feature
- Glossary/terminology -- any new terms introduced by the feature

**Tone:** Aspirational. Use future tense ("This will...") or present tense describing the design ("The gateway routes requests to..."). The docs describe the intended system.

**Output:** Committed doc files on the current branch.

## Post-Implementation Mode

**Source material:** Specification AND actual implementation code.

**What to produce:**

- Revised conceptual overview -- updated to reflect what was actually built
- Polished README -- accurate setup, usage, and examples
- API reference -- from code analysis (actual function signatures, parameter types, return types)
- Configuration reference -- from actual defaults and validation logic
- Tutorials/how-to guides -- from working examples and tested commands

**Tone:** Authoritative. Use present tense ("The gateway routes requests to..."). The docs describe the real system.

**Accuracy requirement:** Every claim must be verified against code:

- Function signatures match actual exports
- Config options match actual defaults and validation
- Commands produce the documented output
- File paths exist and contain what is described

**Output:** Committed doc files on the current branch.

## Phase 1: Read and Orient

1. Read the specification completely
2. Detect mode (pre-impl vs post-impl) per the Phase Detection section above
3. Read `.wingman/rules/DOCUMENTATION.md` for placement and structure standards
4. If post-impl, read the implementation code to understand what was built

## Phase 2: Survey Existing Docs

1. Check what documentation already exists for this feature or related features
2. Read `mkdocs.yml` to understand the current nav structure and section numbering
3. Identify whether new pages are needed or existing pages should be updated
4. Note the style and structure of neighboring docs for consistency

## Phase 3: Determine Scope

Decide what docs are needed based on the feature and mode:

| Doc type | When to create |
|----------|----------------|
| Conceptual/architectural | Always -- every feature needs an explanation of what it is and why |
| README sections | When the feature adds or changes a package's public surface |
| API reference | Post-impl only -- requires actual code to document |
| Tutorials | When the feature has user-facing workflows worth walking through |
| How-to guides | When the feature answers common "how do I..." questions |
| Configuration reference | When the feature introduces configurable options |
| Migration guide | Only at release time for breaking changes |

## Phase 4: Write the Docs

For each doc identified in Phase 3:

1. Determine the correct file location per DOCUMENTATION.md placement rules
2. Write the content following the Doc Type Guidelines below
3. In post-impl mode, verify every technical claim against the code
4. Match the style and conventions of existing docs in the same directory

## Phase 5: Update Navigation

If new pages were added to `docs/`:

1. Update `mkdocs.yml` nav to include the new pages
2. Place them in the correct section following the existing numbering scheme
3. Verify nav entries use correct relative paths

## Phase 6: Commit

Commit all doc files to the current branch with a clear commit message indicating what was documented.

## Doc Type Guidelines

### Conceptual / Architectural

- What the feature is and why it exists
- How it fits into the broader system
- Key design decisions and their rationale
- Component relationships and data flow
- Keep it high-level -- link to API reference for details

### README Sections

- Overview: one paragraph explaining the package/module
- Setup/usage: how to install, configure, and run
- Key APIs: table of exports with one-line descriptions
- Examples: minimal working examples
- Testing: how to run tests (scoped to this package)
- Target under 150 lines total

### API Reference

- Function signatures with parameter types and return types
- Parameter descriptions and constraints
- Usage examples (from actual code in post-impl mode)
- Error conditions and what they throw/return
- Group by module or topic, not alphabetically

### Tutorials

- Step-by-step with real commands and expected output
- Start with prerequisites and end with verification
- Each step should be independently testable
- Include the "why" for each step, not just the "what"

### How-to Guides

- Task-oriented: answer "how do I..."
- Start with the goal, then list steps
- Include common variations and edge cases
- Link to conceptual docs for background

### Configuration Reference

- All options listed with types, defaults, and constraints
- Group by category (not alphabetically)
- Include example configurations for common scenarios
- Note which options require restart vs hot-reload

### Migration Guides

- Only at release time for breaking changes
- Before/after code examples for each breaking change
- Step-by-step migration procedure
- Automated migration scripts when feasible

## Integration with Workflows

### Standalone

User invokes directly. The skill creates/updates docs and commits them.

### In Implementing-Specs Workflow

Called after the EXECUTE phase. Works in the worktree. Docs land on the same PR as the implementation.

## Checklist Before Completion

- [ ] Specification fully read and understood
- [ ] Mode correctly detected (pre-impl vs post-impl)
- [ ] DOCUMENTATION.md standards read and followed
- [ ] Existing docs surveyed for overlap and style
- [ ] Doc placement follows DOCUMENTATION.md rules (cross-cutting in `docs/`, package-level in `README.md`)
- [ ] No content duplicated across files -- links used instead
- [ ] In post-impl mode: all technical claims verified against actual code
- [ ] `mkdocs.yml` nav updated if new pages added to `docs/`
- [ ] All docs committed to the current branch
