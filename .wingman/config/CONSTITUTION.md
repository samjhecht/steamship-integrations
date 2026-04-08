# Wingman Workspace Conventions

## Vision

@.wingman/VISION.md

## Governance Rules

1. **Constitution is supreme law**: This document overrides all other practices and decisions. This `.wingman/config/CONSTITUTION.md` is the top level map of the project's conventions, rules and guiding context. It uses progressive disclosure.
2. **No exceptions**: Principles are non-negotiable unless formally amended via the `amending-constitution` skill
3. **Regular review**: Revisit quarterly to ensure principles still serve the mission

## Directory Schema

| Location | Purpose | Managed By |
| -------- | ------- | ---------- |
| .wingman/issues/ | Issue tracking files | Manual |
| .wingman/specifications/ | Feature specifications | Manual |
| .wingman/notes/ | Notes, reference material, and ideas (replaces memos/ and ideas/) | wingman:writing-notes skill |
| .wingman/memory/ | Agent-managed compacted knowledge base | Manual / future automation |
| .wingman/rules/ | Project rules and standards (coding, testing, security). Delivered to agent context via .claude/rules/ symlinks. | wingman:init-workspace |
| .wingman/plans/ | Implementation plans | wingman:writing-plans skill |
| .wingman/cache/ | Runtime cache | System |
| .wingman/config/ | Configuration files | wingman:init-workspace |
| .wingman/playbooks/ | Playbook assets for prompts, workflows, and templates | wingman:init-workspace |
| .wingman/sessions/ | Session state and history | MCP session tools |
| .wingman/rules/DESIGN.md | Detailed design philosophy and architectural guidelines | Manual |
| .wingman/rules/CODING.md | Language-specific coding conventions and patterns | Manual |
| .wingman/rules/TESTING.md | Test strategy, coverage goals, and testing patterns | Manual |
| .wingman/rules/SECURITY.md | Security policies, data handling, and threat model | Manual |
| .wingman/memory/decisions/ | Architecture Decision Records (ADRs) | adr-skill |
| .wingman/memory/constitution-versions/ | Snapshots of each major constitutional version | amending-constitution skill |

## Intent Routing

| When you need to... | Use this skill/tool |
| ------------------- | ------------------- |
| Write a note, memo, or research | wingman:writing-notes |
| Create or track a task/issue | wingman:creating-issues |
| Write a feature specification | wingman:writing-specifications |
| Create an implementation plan | wingman:writing-plans |
| Run tests | wingman:running-tests |
| Review code | wingman:reviewing-code |
| Commit, push, create PR | wingman:commit-push-pr |
| Run a workflow | wingman:running-workflows |

## Rules & Project Standards

Rules are loaded via `.claude/rules/` symlinks managed by `init_workspace --fix`. Use `rules_list` and `rules_get` MCP tools for programmatic access.

- Always check for and use the relevant skill before creating files in .wingman/
- Notes, memos, and research go in .wingman/notes/ via the writing-notes skill
- Issues and specifications MUST be created and edited via MCP tools (issues_create, issues_update, etc.) -- never via `gh issue create` or hand-editing files, as the MCP tools handle project board linking and field metadata that the CLI does not. When the provider is `github` (check `.wingman/config/wingman.json`), MCP tools create GitHub issues. Use `type: specification` for specs and `type: issue` for tasks.
- The `.wingman/specifications/` and `.wingman/issues/` directories are only used by the markdown provider. When the GitHub provider is active, these directories are not the source of truth -- GitHub is.
- The .wingman/memory/ directory is for reference material and knowledge base content, not for notes

## Governance Rules
- **Wingman Issues are source of truth** for what should be built. PRs reference specs/issues/epics, not the other way around.
- **Tracking files are ephemeral** -- they live in worktree branches, not main.

## Open Questions

High-level project decisions not yet fully resolved. These are tracked as `open-question` type issues so they surface during roadmap reviews and agent meta-analysis. Open Questions could be filed as standalone issues for questions with cross-cutting scope, or as subissues on a spec/epic/issue if they pertain just to that specific scope.

Examples of open questions:

- Unresolved architectural direction (e.g. "When should we prioritize merging project X and Y?")
- Ambiguities in vision or principles that haven't caused problems yet but may
- Contradictions between constitutional principles and detailed standards
- Strategic questions raised by agents during implementation or review

To file an open question: `issues_create({ type: "open-question", ... })`

To review open questions: `issues_list({ type: "open-question", status: ["open"] })`

## Amending The Constitution

To propose changes to constitutional principles, use the `amending-constitution` skill. This creates a `constitutional-amendment` issue with structured fields for rationale, impact analysis, and migration plan. Such issues can be used for suggesting additions or improvements to the project's rules/standards/principles when gaps or contradictions are identified.
