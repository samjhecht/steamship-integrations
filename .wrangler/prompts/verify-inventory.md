---
name: verify-inventory
description: Verify a completed carrier API inventory against quality standards
---

Review the completed API inventory for **{{ carrier.name }}** against the project's quality standards.

## Inputs

Read these files:
1. `docs/carriers/{{ carrier.slug }}/api-inventory.md` -- the completed inventory to review
2. `docs/required-data-fields.md` -- the canonical field reference
3. `docs/carriers/{{ carrier.slug }}/research-notes.md` -- the original research (to check nothing was lost in translation)

## Quality Checklist

Verify each of these. For each item, report pass/fail with specifics:

### Completeness
- [ ] Every field from `required-data-fields.md` sections 1-5 has a row in the coverage matrix
- [ ] All tracking-related endpoints documented (not just the main one -- schedules, bookings, cutoffs, events, etc.)
- [ ] Authentication requirements fully documented -- someone at SSL reading this knows exactly what to do to get API access
- [ ] Lookup methods documented -- which identifiers work with which endpoints
- [ ] DCSA compliance assessed -- member status, which APIs, which version, known deviations
- [ ] Non-API access methods checked -- web portal, EDI, third-party aggregators
- [ ] Community resources checked -- GitHub libraries, npm/PyPI packages, forum discussions
- [ ] Webhook/push support assessed
- [ ] Sandbox/test environment assessed
- [ ] Data freshness assessed
- [ ] SDK adapter recommendations provided -- feasibility, architecture, lookup method, polling strategy, effort estimate
- [ ] Open questions listed

### Accuracy
- [ ] All links verified as real URLs (not fabricated patterns)
- [ ] All quantitative claims have sourcing tiers (Official/Reported/Estimated/Unknown)
- [ ] No unsourced attributions (never "the carrier states X" without a linkable URL)
- [ ] Methodology stated -- status field reflects documentation-only vs. live testing
- [ ] Coverage summary math adds up (available + unavailable + unknown = total per category)
- [ ] Status icons in coverage matrix match the narrative in endpoint documentation

### Cross-Reference with Research Notes
- [ ] No findings from research-notes.md were dropped or lost during template filling
- [ ] Sourcing tiers were preserved (not upgraded from "Reported" to stated-as-fact)
- [ ] Nuances and caveats from research were carried through

## Output

```json
{
  "hasGaps": true,
  "gaps": [
    {
      "location": "Section 3, field 3.5",
      "severity": "critical",
      "description": "Container Discharged at Transshipment has no row in the coverage matrix"
    }
  ],
  "passedChecks": 14,
  "totalChecks": 17,
  "summary": "Overall quality assessment"
}
```
