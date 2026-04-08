# Wingman Workspace Conventions

## Directory Schema
| Directory | Purpose | Managed By |
|-----------|---------|-----------|
| .wingman/notes/ | Notes, reference material, and ideas (replaces memos/ and ideas/) | wingman:writing-notes skill |
| .wingman/memory/ | Agent-managed compacted knowledge base | Manual / future automation |
| .wingman/rules/ | Project rules and standards (coding, testing, security). @imported into agent context. | wingman:init-workspace |
| .wingman/plans/ | Implementation plans | wingman:writing-plans skill |
| .wingman/cache/ | Runtime cache | System |
| .wingman/config/ | Configuration files | wingman:init-workspace |
| .wingman/playbooks/ | Playbook assets for prompts, workflows, and templates | wingman:init-workspace |
| .wingman/sessions/ | Session state and history | MCP session tools |

## Intent Routing
| When you need to... | Use this skill/tool |
|---------------------|---------------------|
| Write a note, memo, or research | wingman:writing-notes |
| Create or track a task/issue | wingman:creating-issues |
| Write a feature specification | wingman:writing-specifications |
| Create an implementation plan | wingman:writing-plans |
| Run tests | wingman:running-tests |
| Review code | wingman:reviewing-code |
| Commit, push, create PR | wingman:commit-push-pr |

## Rules
- Always check for and use the relevant skill before creating files in .wingman/
- Notes, memos, and research go in .wingman/notes/ via the writing-notes skill
- Issues and specifications MUST be created and edited via MCP tools (issues_create, issues_update, etc.) -- never via `gh issue create` or hand-editing files, as the MCP tools handle project board linking and field metadata that the CLI does not.
- The .wingman/memory/ directory is for reference material and knowledge base content, not for notes

## Project Standards
@../rules/index.md
