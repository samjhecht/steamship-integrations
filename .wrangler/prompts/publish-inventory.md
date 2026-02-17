---
name: publish-inventory
description: Write carrier-specific coverage row and update steamship-lines index after inventory completion
---

The API inventory for **{{ carrier.name }}** (SCAC: {{ carrier.scac }}) is complete and verified. Finalize the carrier-specific outputs.

## Tasks

### 1. Update `docs/steamship-lines.md`

Find or add this carrier's row and populate:
- SCAC Code
- Parent / Alliance
- API Status (None / Tracking Only / Full Suite / Portal Only)
- DCSA Compliant? (Yes + version / Partial / No)
- Auth Type
- Developer Portal URL
- Set Audit Status to "Complete"

Read the inventory at `docs/carriers/{{ carrier.slug }}/api-inventory.md` to get the correct values.

### 2. Write `docs/carriers/{{ carrier.slug }}/coverage-row.md`

Create a standalone file containing this carrier's row for the cross-carrier field coverage matrix. This file will be consumed later by a separate aggregation step that builds the full `docs/carrier-field-coverage-matrix.md` from all carriers, avoiding merge conflicts when multiple carrier inventories run in parallel.

The file must contain:
- A single markdown table row matching the column order in `docs/carrier-field-coverage-matrix.md`
- Use the standard status icons: checkmark = Available, lock = Requires partner onboarding, warning = Available with caveats, question mark = Unknown, X = Unavailable
- The row values must be consistent with the field coverage findings in the inventory

Read the existing `docs/carrier-field-coverage-matrix.md` to match the exact column order and icon format. Write just the carrier name and data row (no header), e.g.:

```markdown
| **Carrier Name** | icon | icon | icon | ... |
```

### 3. Clean Up Research Notes

The intermediate research notes at `docs/carriers/{{ carrier.slug }}/research-notes.md` served their purpose as a workflow artifact. Check whether they contain any unique information not captured in the final inventory:
- If the research notes contain information not in the inventory (e.g., additional context, rejected approaches, dead-end investigations), keep them as a supplementary reference
- If all useful information has been captured in the inventory, delete the research notes to avoid stale duplication

## Output

```json
{
  "steamshipLinesUpdated": true,
  "coverageRowPath": "docs/carriers/{slug}/coverage-row.md",
  "researchNotesKept": false,
  "summary": "Brief description of what was updated"
}
```
