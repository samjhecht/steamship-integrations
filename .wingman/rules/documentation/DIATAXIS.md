---
name: Diataxis Documentation Framework
description: Organize documentation into four modes -- tutorials, how-to guides, reference, explanation
category: documentation
---

# Diataxis Documentation Framework

Organize all documentation into four distinct modes based on user need. Each mode has a different purpose, structure, and audience mindset. Do not mix modes within a single document.

## The Four Modes

| Mode | Orientation | User Need | Structure |
|------|-------------|-----------|-----------|
| **Tutorials** | Learning | "Help me get started" | Step-by-step lessons with guaranteed outcomes |
| **How-to Guides** | Task | "Help me accomplish X" | Goal-oriented steps for real-world tasks |
| **Reference** | Information | "Help me look up Y" | Factual, complete, structured for lookup |
| **Explanation** | Understanding | "Help me understand why" | Conceptual narrative, context, rationale |

## Tutorials

- Learning-oriented. Teach by doing.
- Lead the reader through a series of steps to complete a meaningful project.
- Guarantee a successful outcome -- every tutorial must work when followed exactly.
- Explain only what is necessary to complete the step. Save deep explanations for the Explanation quadrant.
- Do not offer choices. Make decisions for the learner.
- Test tutorials regularly. Broken tutorials destroy trust faster than missing tutorials.

## How-to Guides

- Task-oriented. Solve a specific real-world problem.
- Assume the reader already knows the basics (unlike tutorials).
- Title as a verb phrase: "How to configure SSL", "How to migrate from v2 to v3".
- Provide the most direct path to the goal. Skip background context.
- Address one task per guide. If a guide covers multiple tasks, split it.
- Include prerequisite assumptions at the top.

## Reference

- Information-oriented. Describe the system as it is.
- Structure mirrors the codebase: one reference entry per API, CLI command, config option, or type.
- Factual and complete. If it exists in the system, it exists in the reference.
- No tutorials, opinions, or narrative. Just the facts: signature, parameters, return type, defaults, constraints, examples.
- Auto-generate from code where possible (OpenAPI, JSDoc, docstrings). Hand-written reference rots.
- Use consistent formatting across all entries.

## Explanation

- Understanding-oriented. Provide context, rationale, and background.
- Explain the "why" behind design decisions, architecture, and tradeoffs.
- Connect concepts to each other. Show how pieces fit together.
- Free-form narrative is acceptable. This is the one mode where prose and discussion are appropriate.
- ADRs (Architecture Decision Records) are a form of explanation.
- Not tied to specific tasks or steps. Timeless context that helps readers build mental models.

## Rules

- Every document belongs to exactly one quadrant. If you are mixing tutorial steps with reference tables, split the document.
- Label or categorize documents by mode so readers can navigate by intent.
- Reference docs should be auto-generated from source when possible.
- Tutorials and how-to guides must be tested -- if the steps do not produce the promised result, the document is broken.
- Explanation docs are the bridge between pre- and post-implementation: start them from the spec, refine from the code.
