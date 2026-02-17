---
name: fix-inventory-gaps
description: Fix specific gaps identified in inventory verification
---

Fix the identified gaps in the API inventory for **{{ carrier.name }}**.

## Inputs

1. `docs/carriers/{{ carrier.slug }}/api-inventory.md` -- the inventory to fix
2. `docs/carriers/{{ carrier.slug }}/research-notes.md` -- the original research for reference
3. `docs/required-data-fields.md` -- the canonical field reference

## Gaps to Fix

{{ #each review.gaps }}
### {{ this.severity }}: {{ this.location }}
{{ this.description }}
{{ /each }}

## Rules

- Fix only the identified gaps -- do not restructure or rewrite unrelated sections
- If a gap requires information not present in the research notes, mark the field with "Unknown" and add it to the Open Questions section
- Preserve all sourcing tiers -- do not upgrade confidence without additional evidence
- If the research notes contain information that addresses a gap, use it with proper attribution
- After fixing, verify the coverage summary math still adds up

## Output

Write the updated inventory to `docs/carriers/{{ carrier.slug }}/api-inventory.md`.

```json
{
  "gapsFixed": 3,
  "gapsUnresolvable": 1,
  "unresolvableReasons": ["No data available in research notes for field X"],
  "inventoryPath": "docs/carriers/{slug}/api-inventory.md"
}
```
