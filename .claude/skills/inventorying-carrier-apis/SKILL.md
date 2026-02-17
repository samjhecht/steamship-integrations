---
name: inventorying-carrier-apis
description: Workflow-orchestrated research and documentation of ocean carrier API capabilities. Produces a verified inventory, updates cross-carrier reference docs, and provides SDK adapter recommendations. Uses session tracking for crash recovery.
---

# Inventorying Carrier APIs

## Overview

Research and document all API capabilities and tracking data available from a single ocean carrier. This is a **workflow skill** that coordinates multiple agents across five phases, producing a verified API inventory as the primary deliverable.

**Workflow definition**: `.wrangler/workflows/carrier-api-inventory.yaml`

**Output**: A verified `api-inventory.md` in `docs/carriers/{carrier}/`, a `coverage-row.md` with this carrier's matrix row, plus updates to `docs/steamship-lines.md`. The cross-carrier coverage matrix (`docs/carrier-field-coverage-matrix.md`) is built separately from all carriers' `coverage-row.md` files to avoid merge conflicts when running inventories in parallel.

## Before You Start

1. **Read `docs/required-data-fields.md`** -- the canonical list of fields SSL needs. This is the completeness standard every inventory is measured against.
2. **Read the spec** at `.wrangler/specifications/steamship-line-api-audit.md` -- understand the broader project context.
3. **Read the template** at `.claude/skills/inventorying-carrier-apis/templates/carrier-api-inventory.md` -- understand every section the inventory must cover.

## Workflow Phases

```
Phase 1: RESEARCH (researcher agent)
    Web research -- find all APIs, docs, endpoints, auth, community resources.
    Output: docs/carriers/{carrier}/research-notes.md
    |
Phase 2: INVENTORY (implementer agent)
    Fill the inventory template from research notes + required-data-fields.md.
    Output: docs/carriers/{carrier}/api-inventory.md
    |
Phase 3: VERIFY (inventory-reviewer agent)
    Review inventory against quality checklist. Fresh agent with no "I just wrote this" bias.
    Output: pass/fail with specific gaps
    |
Phase 4: FIX (conditional, single pass)
    If verify found gaps: implementer fixes them.
    Skipped entirely if Phase 3 found no issues.
    |
Phase 5: PUBLISH (implementer agent)
    Update docs/steamship-lines.md with carrier row.
    Write docs/carriers/{carrier}/coverage-row.md with this carrier's matrix data.
    Clean up research notes if all useful info was captured in inventory.
```

## Execution Protocol

### Session Setup

```
1. Call session_start with workflow: "carrier-api-inventory"
2. Capture sessionId and worktreePath
3. Set carrier context variables: name, scac, slug (lowercase-hyphenated name)
```

### Phase 1: Research

Dispatch a subagent with:
- **Agent type**: `general-purpose` (needs WebSearch + WebFetch)
- **Prompt**: Contents of `.wrangler/prompts/research-carrier.md` with carrier variables filled
- **Key instruction**: Write findings to `docs/carriers/{slug}/research-notes.md`

After completion:
- Call `session_checkpoint` with phase 1 results
- Verify `research-notes.md` was created and has content

### Phase 2: Inventory

Dispatch a subagent with:
- **Agent type**: `general-purpose` (needs Read + Write)
- **Prompt**: Contents of `.wrangler/prompts/fill-inventory.md` with carrier variables filled
- **Key inputs**: research-notes.md, template, required-data-fields.md

After completion:
- Call `session_checkpoint` with phase 2 results
- Verify `api-inventory.md` was created

### Phase 3: Verify

Dispatch a subagent with:
- **Agent type**: `general-purpose` (needs Read only)
- **Prompt**: Contents of `.wrangler/prompts/verify-inventory.md` with carrier variables filled
- **Key inputs**: api-inventory.md, required-data-fields.md, research-notes.md

Evaluate the response:
- If `hasGaps == false`: proceed to Phase 5
- If `hasGaps == true`: proceed to Phase 4

### Phase 4: Fix (conditional, single pass)

Only runs if Phase 3 found gaps.

1. Dispatch implementer subagent with gap list from verify step
2. Subagent fixes what it can, marks unresolvable gaps as "Unknown" with Open Questions

### Phase 5: Publish

Dispatch a subagent with:
- **Agent type**: `general-purpose` (needs Read + Write)
- **Prompt**: Contents of `.wrangler/prompts/publish-inventory.md` with carrier variables filled

After completion:
- Call `session_complete` with final status
- Report summary to user

## Carriers Without APIs

Some carriers (particularly smaller or regional ones) may have no public API. The workflow still applies:

- **Phase 1 (Research)** will discover the absence and document alternatives (portal, EDI, aggregators)
- **Phase 2 (Inventory)** will delete inapplicable endpoint sections and focus on what IS available
- **Phase 3 (Verify)** will check that the absence was properly documented, not just overlooked
- **Phase 5 (Publish)** will write a coverage-row.md and update steamship-lines.md with appropriate "None" / "Portal Only" status

## Common Carrier Patterns

| Pattern | Examples | SDK Approach |
|---------|----------|-------------|
| Full DCSA compliance | Maersk, Hapag-Lloyd | DCSA base adapter -- minimal custom mapping |
| Partial DCSA + proprietary extensions | Various | DCSA base adapter + custom overrides |
| Proprietary API, no DCSA | Grimaldi | Fully custom adapter with carrier-specific mapping |
| No API, portal only | Smaller carriers | Mark unsupported or evaluate scraping/aggregator |
| No API, available via aggregator | Various | Consider aggregator integration instead of direct |

## Quality Checklist (enforced by Phase 3)

- [ ] `required-data-fields.md` fully mapped -- every field has a row with status icon
- [ ] All tracking-related endpoints documented
- [ ] Authentication requirements fully documented
- [ ] Rate limits documented with source tier
- [ ] Lookup methods documented
- [ ] DCSA compliance assessed with version numbers
- [ ] Non-API access methods checked (portal, EDI, aggregators)
- [ ] Community resources checked
- [ ] Webhook/push support assessed
- [ ] Sandbox/test environment assessed
- [ ] Data freshness assessed
- [ ] All links verified working
- [ ] All quantitative claims have sourcing tiers
- [ ] No unsourced attributions
- [ ] Methodology stated
- [ ] SDK adapter recommendations provided
- [ ] Open questions listed
- [ ] `docs/steamship-lines.md` updated
- [ ] `docs/carriers/{carrier}/coverage-row.md` written with matrix row data

## Crash Recovery

If interrupted, run `session_get()` (no sessionId) to find the most recent incomplete session. The checkpoint's `resumeInstructions` field tells you where to pick up:

- **Interrupted during Phase 1**: Re-run research (web research is not idempotent -- start fresh)
- **Interrupted during Phase 2**: Check if `research-notes.md` exists. If yes, re-run Phase 2 only
- **Interrupted during Phase 3 or 4**: Check if `api-inventory.md` exists. If yes, re-run verify
- **Interrupted during Phase 5**: Check if inventory is verified. If yes, re-run publish only

## Related Skills

- `researching-web-sources` -- used by the researcher agent in Phase 1
- `writing-specifications` -- if the carrier adapter needs a spec before implementation
