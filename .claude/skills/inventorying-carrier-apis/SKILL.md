---
name: inventorying-carrier-apis
description: Workflow-orchestrated research and documentation of ocean carrier API capabilities. Produces a verified inventory, updates cross-carrier reference docs, and provides SDK adapter recommendations.
---

# Inventorying Carrier APIs

## Overview

Research and document all API capabilities and tracking data available from a single ocean carrier. This is a **workflow skill** -- the workflow engine handles phase sequencing, agent dispatch, and checkpointing automatically.

**Workflow definition**: `.wrangler/orchestration/workflows/carrier-api-inventory.yaml`

## How to Run

Start the workflow with `session_start`:

```
session_start(
  specFile: ".wrangler/orchestration/workflows/carrier-api-inventory.yaml",
  workflow: "carrier-api-inventory"
)
```

The workflow needs these carrier context variables:
- **name**: Full carrier name (e.g. "Maersk Line")
- **scac**: SCAC code (e.g. "MAEU")
- **slug**: Lowercase hyphenated name for directory paths (e.g. "maersk")

## What the Workflow Produces

Per carrier:
- `docs/carriers/{slug}/api-inventory.md` -- the primary deliverable (verified inventory)
- `docs/carriers/{slug}/coverage-row.md` -- this carrier's row for the cross-carrier coverage matrix
- `docs/carriers/{slug}/research-notes.md` -- intermediate research artifact (may be cleaned up by publish step)
- Updated row in `docs/steamship-lines.md`

The cross-carrier coverage matrix (`docs/carrier-field-coverage-matrix.md`) is **not** updated by this workflow. It is built separately by aggregating all carriers' `coverage-row.md` files, avoiding merge conflicts when running multiple carrier inventories in parallel.

## Workflow Phases

The engine runs these phases sequentially. You do not need to dispatch them manually.

| Phase | Agent | What happens |
|-------|-------|-------------|
| **research** | researcher | Web research: finds all APIs, docs, endpoints, auth, community resources. Writes `research-notes.md` |
| **inventory** | implementer | Fills the inventory template from research notes + `required-data-fields.md`. Writes `api-inventory.md` |
| **verify** | inventory-reviewer | Reviews inventory against quality checklist with fresh context (no writer bias) |
| **fix** | implementer | If verify found gaps: fixes them in a single pass. Skipped if no gaps found |
| **publish** | implementer | Updates `steamship-lines.md`, writes `coverage-row.md`, cleans up research notes |

## Reference Documents

These are used by the workflow's agents (defined in the prompt files), listed here for context:
- `docs/required-data-fields.md` -- canonical field reference. Every field must have a row in the inventory
- `.wrangler/specifications/steamship-line-api-audit.md` -- project spec
- `.claude/skills/inventorying-carrier-apis/templates/carrier-api-inventory.md` -- inventory template

## Carriers Without APIs

The workflow handles API-less carriers gracefully:
- Research discovers the absence and documents alternatives (portal, EDI, aggregators)
- Inventory deletes inapplicable endpoint sections, focuses on what IS available
- Verify checks that the absence was properly documented, not just overlooked
- Publish writes coverage-row.md and updates steamship-lines.md with "None" / "Portal Only" status

## Common Carrier Patterns

| Pattern | Examples | SDK Approach |
|---------|----------|-------------|
| Full DCSA compliance | Maersk, Hapag-Lloyd | DCSA base adapter -- minimal custom mapping |
| Partial DCSA + proprietary extensions | Various | DCSA base adapter + custom overrides |
| Proprietary API, no DCSA | Grimaldi | Fully custom adapter with carrier-specific mapping |
| No API, portal only | Smaller carriers | Mark unsupported or evaluate scraping/aggregator |
| No API, available via aggregator | Various | Consider aggregator integration instead of direct |

## Parallel Execution

Multiple carrier inventories can run simultaneously since each writes only to its own `docs/carriers/{slug}/` directory. The only shared file touched is `docs/steamship-lines.md` (during publish), which has one row per carrier and is low-conflict.

After all parallel inventories complete, aggregate the coverage matrix:
1. Read each carrier's `docs/carriers/{slug}/coverage-row.md`
2. Assemble into `docs/carrier-field-coverage-matrix.md` with the standard header
