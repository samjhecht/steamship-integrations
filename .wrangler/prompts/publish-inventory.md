---
name: publish-inventory
description: Update cross-carrier reference documents after inventory completion
---

The API inventory for **{{ carrier.name }}** (SCAC: {{ carrier.scac }}) is complete and verified. Update the cross-carrier reference documents.

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

### 2. Update `docs/carrier-field-coverage-matrix.md`

Add a row for this carrier covering all columns in the matrix. Use the standard status icons:
- Available: checkmark
- Requires partner onboarding: lock icon
- Available with caveats: warning icon
- Unknown: question mark icon
- Unavailable: X icon

The row values must be consistent with the field coverage findings in the inventory. Read the existing matrix to match the exact column order and icon format used by other carriers.

### 3. Clean Up Research Notes

The intermediate research notes at `docs/carriers/{{ carrier.slug }}/research-notes.md` served their purpose as a workflow artifact. Check whether they contain any unique information not captured in the final inventory:
- If the research notes contain information not in the inventory (e.g., additional context, rejected approaches, dead-end investigations), keep them as a supplementary reference
- If all useful information has been captured in the inventory, delete the research notes to avoid stale duplication

## Output

```json
{
  "steamshipLinesUpdated": true,
  "coverageMatrixUpdated": true,
  "researchNotesKept": false,
  "summary": "Brief description of what was updated"
}
```
