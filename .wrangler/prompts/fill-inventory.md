---
name: fill-inventory
description: Fill the carrier API inventory template from research notes
---

Create a complete API inventory for **{{ carrier.name }}** using the research notes from the previous step.

## Inputs

Read these files:
1. `docs/carriers/{{ carrier.slug }}/research-notes.md` -- the research findings from the previous workflow step
2. `.claude/skills/inventorying-carrier-apis/templates/carrier-api-inventory.md` -- the inventory template
3. `docs/required-data-fields.md` -- the canonical field reference (every field must have a row)

## Instructions

Copy the template to `docs/carriers/{{ carrier.slug }}/api-inventory.md` and fill every section using the research notes as your primary source.

### Key Rules

- **Every field from `docs/required-data-fields.md` must have a row in the coverage matrix** -- use "Unknown" for genuinely unknown, "Not available" for confirmed unavailable, never leave blank
- **Delete inapplicable sections** -- if the carrier has no API, delete endpoint reference sections; if no webhook support, delete that section
- **Add rows/sections as discovered** -- the template is a starting point, not a ceiling
- **Include gotchas prominently** -- hard limits, beta status, deprecated endpoints, data quality issues
- **Links must be real** -- only include URLs that appeared in the research notes with verification
- **Sourcing tiers must be preserved** -- carry forward the Official/Reported/Estimated/Unknown labels from the research notes

### Methodology Transparency

The inventory's status field must accurately reflect the research methodology:
- State: "Documentation review only -- not verified with live API testing" (typical case)
- If the research notes mention a publicly accessible endpoint that was tested, note what was observed
- Never imply live verification when only docs were consulted

### Coverage Assessment

After filling the template, compute the coverage summary:

| Question | Why it matters |
|----------|---------------|
| What % of required fields does this carrier's API cover? | Sets expectations for adapter completeness |
| Which critical fields are missing? | Identifies deal-breakers or workarounds needed |
| Is the API DCSA-compliant enough to use a shared base adapter? | Affects SDK architecture |
| What data is only available through the web portal? | Identifies gaps needing portal scraping or manual processes |
| Are there fields available through aggregators that the direct API doesn't expose? | May influence integration strategy |

### SDK Adapter Recommendations

Based on the inventory, include recommendations for:

1. **Adapter feasibility** -- Can we build a useful adapter? Or is the API too limited?
2. **Adapter architecture** -- DCSA base class (if compliant) or fully custom? How much mapping work?
3. **Recommended lookup method** -- Which shipment identifier type works best?
4. **Recommended polling strategy** -- How often to poll? Cursor/timestamp filters for incremental polling?
5. **Auth implementation notes** -- OAuth flow details, token refresh patterns, credential requirements
6. **Edge cases** -- Transshipment handling, multi-container shipments, vessel changes, rolled containers
7. **Implementation effort** -- Low (DCSA passthrough) / Medium (DCSA + deviations) / High (proprietary) / Very High (no API)

## Output

Write the completed inventory to `docs/carriers/{{ carrier.slug }}/api-inventory.md`.

```json
{
  "inventoryPath": "docs/carriers/{slug}/api-inventory.md",
  "coveragePercent": 85,
  "criticalFieldsMissing": ["field1", "field2"],
  "adapterFeasibility": "yes/partial/no",
  "implementationEffort": "low/medium/high/very-high",
  "openQuestions": ["question 1", "question 2"]
}
```
