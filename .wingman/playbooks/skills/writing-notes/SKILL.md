---
name: writing-notes
description: Creates structured notes from user intent. Replaces ad-hoc memo writing and the former capturing-ideas skill. Auto-selects a typed template based on context.
---

# Writing Notes

## Purpose

This skill creates structured, Obsidian-compatible notes stored in `.wingman/notes/`. It replaces the former `capturing-ideas` skill and absorbs ad-hoc memo writing into a single, unified workflow with typed templates.

"Notes" is now the canonical term. "Memos" and "ideas" are absorbed into note types.

## Trigger Phrases

Activate this skill when the user says anything like:

- "Write a memo"
- "Save a note"
- "Save an idea"
- "Research and write it up"
- "I need to think through..."
- "Let me capture this..."
- "Write up my thoughts on..."
- "Draft a strategy for..."
- "I have a question about..."

## Note Types

There are 5 note types, each with a dedicated template:

| Type | Template | Use When |
|------|----------|----------|
| **strategy** | `strategy.md` | Evaluating options, making decisions, comparing approaches |
| **idea** | `idea.md` | Capturing a concept, feature thought, or raw proposal |
| **research** | `research.md` | Recording intermediate research results -- sources consulted, structured findings with evidence, implications for the work at hand |
| **open-question** | `open-question.md` | Framing a question that needs answering, with constraints and options |
| **other** | `other.md` | Anything that does not fit the above types -- general-purpose fallback |

## Template Selection

Auto-select the template based on user intent:

1. **strategy** -- User is weighing options or making a decision. Keywords: "should we", "compare", "tradeoffs", "pros and cons", "which approach".
2. **idea** -- User is sharing a raw concept or proposal. Keywords: "what if", "it would be nice", "I've been thinking", "idea for".
3. **research** -- User wants to gather and record facts/findings during investigation (what did we learn?). Contrast with **strategy**, which is about weighing options and making decisions (what should we do?). Keywords: "look into", "research", "investigate", "find out", "write up findings".
4. **open-question** -- User has a question that needs structured exploration. Keywords: "how should we", "what's the best way", "I'm not sure about", "need to figure out".
5. **other** -- Default fallback when none of the above clearly match.

If uncertain, prefer **other** over guessing wrong.

## Frontmatter Schema

All note templates share a standardized frontmatter schema. Every field listed below is **required** in the output (even if left empty). This consistency enables downstream tooling, search, and future knowledge distillation.

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `type` | string | yes | Always `"note"` |
| `noteType` | string | yes | One of: `strategy`, `idea`, `research`, `open-question`, `other` |
| `title` | string | yes | Concise, descriptive title |
| `date` | string | yes | `YYYY-MM-DD` format |
| `tags` | string[] | yes | Topical tags (can be empty `[]`) |
| `status` | string | yes | `draft` or `complete` -- all notes start as `draft` |
| `relatedSpec` | string | no | Spec or issue ID (e.g., `#36` for GitHub or `SPEC-000036` for markdown) if this note relates to a specification. Leave empty string `""` if none |
| `createdBy` | string | yes | Who authored the note -- use `"agent"` for agent-created notes, or the user's name if they authored it |
| `project` | string | no | GitHub repo identifier (e.g., `bacchus-labs/wingman`) to associate the note with a specific project. Omit or leave empty string `""` if the note is not project-specific |

## Process

### 1. Identify Note Type

Read the user's message and select the best-matching template from the table above.

### 2. Fill Template

- Replace `{{TITLE}}` with a concise, descriptive title derived from the user's intent.
- Replace `{{DATE}}` with the current date in `YYYY-MM-DD` format.
- Replace `{{AUTHOR}}` with `"agent"` if the agent is writing the note, or the user's name if they authored it.
- Replace `{{CONTENT}}` (in the "other" template) with the note body.
- Fill in the section headings with relevant content from the conversation.
- Add tags in the frontmatter if obvious from context (e.g., `["architecture"]`, `["ui"]`).
- Set `status` to `draft` initially. Update to `complete` when the note's purpose is fulfilled (research concluded, question answered, decision made).
- Populate `relatedSpec` with the spec or issue ID if the note was prompted by or relates to specific spec work. Leave as empty string if none.
- Populate `project` with the GitHub `owner/repo` identifier if the note relates to a specific project. Infer from the current working directory's git remote when obvious. Omit if not project-specific.

### 3. Save the Note

Save the completed note to `.wingman/notes/` with the filename pattern:

```
.wingman/notes/YYYY-MM-DD-<slug>.md
```

Where `<slug>` is a short, lowercase, hyphen-separated descriptor derived from the title.

### 4. Confirm

Tell the user:
- What note type was selected and why
- The file path where the note was saved
- A brief summary of what was captured

## Guidelines

- **Preserve user voice**: For idea-type notes especially, keep the user's original wording. Do not over-interpret or add technical details they did not mention.
- **Keep it concise**: Notes are working documents, not polished essays. Capture the essentials.
- **One note per intent**: If the user has multiple distinct thoughts, create separate notes.
- **Do not over-structure**: Use the template sections as guidance, not rigid requirements. Leave sections empty if they do not apply rather than filling them with filler.

## Storage

Notes are stored in `.wingman/notes/` (not `.wingman/ideas/`). This is a wingman-native directory, separate from the wingman governance workspace.

## Relationship to Other Skills

- **capturing-ideas** (deprecated) -- Replaced by the `idea` note type in this skill.
- **writing-specifications** -- Use when an idea has matured into a concrete feature requirement.
- **creating-issues** -- Use for actionable work items, not for capturing thoughts.
- **writing-plans** -- Use for implementation plans tied to specifications.

## Templates

Templates are located in `templates/` adjacent to this file. During workspace initialization, they are copied to `.wingman/playbooks/templates/notes/` for user customization.
