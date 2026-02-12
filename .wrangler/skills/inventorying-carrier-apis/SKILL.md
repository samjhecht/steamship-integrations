---
name: inventorying-carrier-apis
description: Research and document all API capabilities and tracking data available from an ocean carrier. Produces a structured inventory covering endpoints, field coverage, authentication, rate limits, and SDK implementation guidance. Designed for parallel execution — one agent per carrier.
---

# Inventorying Carrier APIs

## Overview

Systematically research and document ALL programmatic access methods for a single ocean carrier's shipment tracking data. The output is a complete API inventory that serves as the authoritative reference for building an SDK adapter for that carrier.

**Output**: A completed `api-inventory.md` in `docs/carriers/{carrier}/`.

**Reference documents**:
- `docs/required-data-fields.md` — The canonical list of fields SSL needs, with DCSA event code mappings. Every field in this document must have a row in your coverage matrix.
- `docs/steamship-lines.md` — Carrier index to update with your findings.

## Before You Start

1. **Read `docs/required-data-fields.md` completely** — you need to understand every field, its DCSA mapping, and why SSL needs it. This is your checklist.
2. **Read the spec** at `.wrangler/specifications/steamship-line-api-audit.md` — understand the broader project context.
3. **Read the template** at `.wrangler/skills/inventorying-carrier-apis/templates/carrier-api-inventory.md` — understand every section you need to fill.

## Process

### Phase 1: Reconnaissance (Web Research)

Research the carrier using web sources. Your goal is to map the carrier's entire digital/API ecosystem.

**1. Developer Portal & API Documentation**
- Find the carrier's developer portal (if it exists)
- Find all API documentation — official docs, Swagger/OpenAPI specs, developer guides
- Note documentation quality: is it comprehensive, outdated, sparse?
- Check if the carrier publishes an OpenAPI/Swagger specification file — if so, download and reference it
- Note whether the developer portal is publicly accessible or requires registration just to view docs

**2. DCSA Compliance**
- Determine whether the carrier is a DCSA member
- Identify which DCSA APIs they implement: Track & Trace (TNT), Booking (BKG), Commercial Schedules (CS), eDocumentation (EDoc)
- Note the DCSA version(s) supported
- Search for any published information about DCSA deviations or extensions
- Check the DCSA website and GitHub repos (`github.com/dcsaorg`) for any carrier-specific adoption notes

**3. Authentication & Access Requirements**
- What auth mechanism does the API use? (OAuth2, API key, bearer token, basic auth, client certificates)
- Is developer registration free and self-service, or does it require a business relationship / partner agreement?
- Does the carrier distinguish between "test/sandbox" and "production" access?
- Are there different access tiers (free, partner, enterprise)?
- What credentials are needed? (client ID/secret, API key, username/password)

**4. Tracking / Shipment Visibility Endpoints**
- Find ALL endpoints related to shipment tracking, container tracking, vessel tracking
- For each endpoint, document: HTTP method, path, supported lookup parameters (B/L number, booking reference, container number, vessel IMO)
- Search for endpoints beyond the obvious tracking ones — some carriers expose separate endpoints for:
  - Vessel schedules
  - Port schedules
  - Booking status
  - Shipping instruction status
  - Container events
  - Transport events (vessel movements)
  - Cutoff times
  - Route/routing information

**5. Alternative / Non-API Access Methods**
- Does the carrier have a web portal for manual tracking? Document the URL
- Does the carrier support EDI (Electronic Data Interchange) for tracking? (IFTMIN, COPARN, BAPLIE, etc.)
- Is the carrier available through third-party aggregator platforms? Check for:
  - **project44** — check if carrier is in their network
  - **FourKites** — check if carrier is in their network
  - **Vizion** — check if carrier is in their network
  - **INTTRA** (now part of E2open) — check if carrier is on platform
  - **CargoSmart** — check if carrier is connected
- Does the carrier send email notifications for shipment events? Are those structured or free-text?
- Does the carrier offer any data export / download functionality?

**6. Community & Third-Party Resources**
- Search GitHub for community client libraries or API wrappers for this carrier
- Check npm, PyPI for published packages
- Search forums, Reddit, Stack Overflow for developer experiences with this carrier's API
- Look for blog posts, conference talks, or case studies about integrating with this carrier
- Check if the carrier participates in any industry data-sharing initiatives beyond DCSA

**Endpoint discovery checklist** (don't stop at the main docs page):
- Browse the **full API reference listing**, not just "Getting Started" guides
- If the carrier has an OpenAPI spec, parse it for all paths — especially look for paths containing: `/track`, `/event`, `/shipment`, `/booking`, `/schedule`, `/vessel`, `/container`, `/transport`
- Check community client libraries — their endpoint/method lists often include endpoints the main docs page doesn't highlight
- Search for **recently added endpoints** in changelogs, developer blogs, and release notes
- Look for **variant endpoints** — some carriers have parallel endpoints for different lookup methods (track by B/L vs. track by container vs. track by booking)
- Check if the carrier has **versioned APIs** with newer versions exposing additional data

**Research tips:**
- Search `site:reddit.com "{carrier name} API"` for developer experiences
- Search `"{carrier name}" tracking API developer` for integration guides
- Search `"{carrier name}" DCSA` for standards compliance info
- Check `{carrier name} API changelog` or `{carrier name} developer blog` for recent changes
- Search for the carrier's **SCAC code** — some API docs reference carriers by SCAC rather than name
- If the carrier is part of an alliance (2M, THE Alliance, Ocean Alliance), check if the alliance has shared API infrastructure

### Phase 2: Deep Documentation Review

Once you've found the carrier's API documentation:

1. **Map every endpoint to our required fields** — For each field in `docs/required-data-fields.md`, find the corresponding endpoint and field/event code in this carrier's API. If DCSA-compliant, note where they follow the standard and where they deviate.

2. **Document response shapes** — For key tracking endpoints, document the response structure. Note which fields are always present vs. optional/conditional.

3. **Document lookup methods** — Which shipment identifiers does each endpoint accept? (B/L number, booking reference, container number, carrier booking number). Are there different endpoints for different lookup types?

4. **Document pagination** — How does the API paginate results? Cursor-based, offset-based, or no pagination? What are the page size limits?

5. **Document rate limits** — What are the documented rate limits? Per-endpoint or global? What happens when you hit them (429 response? retry-after header?)? Are there different tiers?

6. **Document webhook/push support** — Does the carrier support event subscriptions? What events can be subscribed to? What's the callback format? Is it reliable?

7. **Document data freshness** — How quickly do events appear in the API after they occur in the physical world? Is this documented, or do you need to infer from user reports?

8. **Check for sandbox/test environment** — Can we develop and test against a non-production environment? What test data is available?

### Phase 3: Fill the Template

Use the template at `.wrangler/skills/inventorying-carrier-apis/templates/carrier-api-inventory.md`.

**Key rules:**

- **Every field from `docs/required-data-fields.md` must have a row in the coverage matrix** — use ❓ for genuinely unknown, ❌ for confirmed unavailable, never leave blank
- **Delete inapplicable sections** — if the carrier has no API, delete endpoint reference sections; if no webhook support, delete that section
- **Add rows/sections as discovered** — the template is a starting point, not a ceiling
- **Include gotchas prominently** — hard limits, beta status, deprecated endpoints, data quality issues
- **Links must be real** — verify every URL loads. Follow redirects and use the **final destination URL**

**Methodology transparency**: The inventory's status field and a note at the top must accurately reflect the research methodology:
- State: "Documentation review only — not verified with live API testing" (this will be the case for most carriers since we don't have credentials yet)
- If you find a publicly accessible endpoint (some carriers have unauthenticated tracking), note what you observed
- Never imply live verification when only docs were consulted

### Sourcing and Confidence Requirements

Every quantitative claim (rate limits, response times, data freshness, event delay) must carry a confidence tier:

| Tier | Label | Use when | Example |
|------|-------|----------|---------|
| **Official** | State as fact | Published in carrier's own docs with a linkable URL | "Rate limit: 100 req/min ([docs](url))" |
| **Reported** | "Reported as ~X" | From developer forums, Reddit, community testing; include source | "Events appear within ~15 min (reported by [user](url))" |
| **Estimated** | "Estimated ~X (unverified)" | Your inference from indirect evidence; no primary source | "Rate limit: estimated ~30 req/min (unverified)" |
| **Unknown** | Use ❓ | No evidence at all | ❓ |

**Rules:**
- If the carrier publishes **no official numbers**, say "Unpublished" — do NOT invent a specific number
- If you cannot find the **specific page** that states a fact, do not attribute it to "the carrier's docs" — say "reported by developers" or "could not verify source"
- **When simplifying complex systems, state the exceptions**: If a rate limit system has different limits per endpoint, list the exceptions or note "with per-endpoint variations — see [link]"
- **Deprecation claims must be precise**: State exactly what is deprecated and link to the notice
- **Version claims must be precise**: Don't say "supports DCSA TNT" without specifying which version

### Phase 4: Coverage Assessment

After filling the template, produce a summary assessment:

| Question | Why it matters |
|----------|---------------|
| What % of our required fields does this carrier's API cover? | Sets expectations for adapter completeness |
| Which critical fields (🔴 in required-data-fields.md) are missing? | Identifies deal-breakers or workarounds needed |
| Is the API DCSA-compliant enough to use a shared base adapter? | Affects SDK architecture |
| What data is only available through the web portal? | Identifies gaps that may need portal scraping or manual processes |
| Are there fields available through third-party aggregators that the carrier's own API doesn't expose? | May influence whether to integrate directly or through an aggregator |

### Phase 5: SDK Adapter Recommendations

Based on the inventory, recommend:

1. **Adapter feasibility** — Can we build a useful adapter for this carrier? Or is the API too limited / nonexistent?
2. **Adapter architecture** — DCSA base class (if compliant) or fully custom? How much mapping work?
3. **Recommended lookup method** — Which shipment identifier type works best? (Some carriers have richer data for B/L lookups vs. container lookups)
4. **Recommended polling strategy** — How often to poll? Are there cursor/timestamp filters for incremental polling?
5. **Auth implementation notes** — OAuth flow details, token refresh patterns, credential requirements
6. **Edge cases** — Transshipment handling, multi-container shipments, vessel changes, rolled containers
7. **Implementation effort estimate** — Relative complexity: Low (DCSA passthrough), Medium (DCSA with deviations), High (fully proprietary), Very High (no API / scraping)

## Output Location

```
docs/carriers/{carrier}/
├── api-inventory.md               # The completed template (primary deliverable)
└── {supplementary-notes}.md       # Optional: deep dives on specific topics
```

After completing the inventory, **update `docs/steamship-lines.md`** with the carrier's row data:
- SCAC Code
- Parent / Alliance
- API Status (None / Tracking Only / Full Suite / Portal Only)
- DCSA Compliant? (Yes + version / Partial / No)
- Auth Type
- Developer Portal URL
- Set Audit Status to "Complete"

## Quality Checklist

Before marking complete:

- [ ] **`required-data-fields.md` fully mapped** — every field from sections 1-5 has a row in the coverage matrix with ✅/❌/❓ status
- [ ] **All tracking-related endpoints documented** — not just the main tracking endpoint, but schedules, bookings, cutoffs, events, etc.
- [ ] **Authentication requirements fully documented** — someone at SSL reading this knows exactly what they need to do to get API access
- [ ] **Rate limits documented with source tier** — Official/Reported/Estimated/Unknown
- [ ] **Lookup methods documented** — which identifiers work with which endpoints
- [ ] **DCSA compliance assessed** — member status, which APIs, which version, known deviations
- [ ] **Non-API access methods checked** — web portal, EDI, third-party aggregators (project44, FourKites, Vizion, INTTRA, CargoSmart)
- [ ] **Community resources checked** — GitHub libraries, npm/PyPI packages, forum discussions
- [ ] **Webhook/push support assessed** — available? what events? callback format?
- [ ] **Sandbox/test environment assessed** — available? how to access? test data?
- [ ] **Data freshness assessed** — how quickly do events appear in the API?
- [ ] **All links verified working** — no redirect-only URLs, all docs URLs load
- [ ] **All quantitative claims have sourcing tiers** — rate limits, data freshness labeled Official/Reported/Estimated/Unknown
- [ ] **No unsourced attributions** — never say "the carrier states X" without a linkable URL
- [ ] **Methodology stated** — status field reflects documentation-only vs. live testing
- [ ] **SDK adapter recommendations provided** — feasibility, architecture, lookup method, polling strategy, effort estimate
- [ ] **Open questions listed** — what couldn't be determined, what needs SSL input, what needs live testing
- [ ] **`docs/steamship-lines.md` updated** — carrier's row populated with findings

## Carriers Without APIs

Some carriers (particularly smaller or regional ones) may have no public API. For these:

- **Confirm the absence** — search thoroughly before concluding; some carriers hide their APIs behind partner portals
- **Document the web portal tracking** — URL, what data it shows, whether it requires login
- **Check third-party aggregators** — the carrier may be accessible through project44, FourKites, Vizion, or INTTRA even if they don't have their own API
- **Check for EDI** — some carriers support EDI messaging even without a REST/SOAP API
- **Assess scraping feasibility** — is the portal scrapable? Does it use anti-bot measures? What are the ToS?
- **Document the gap clearly** — so that the SDK team can make an informed decision about whether to support this carrier and how

## Common Carrier Patterns

| Pattern | Examples | SDK Approach |
|---------|----------|-------------|
| Full DCSA compliance | Maersk, MSC (likely), Hapag-Lloyd (likely) | DCSA base adapter — minimal custom mapping |
| Partial DCSA + proprietary extensions | Various | DCSA base adapter + custom overrides for extensions |
| Proprietary API, no DCSA | Some regional carriers | Fully custom adapter with carrier-specific mapping |
| No API, portal only | Smaller carriers, possibly Grimaldi | Mark unsupported or evaluate scraping/aggregator |
| No API, available via aggregator | Various | Consider aggregator integration instead of direct |

## Related Skills

- `researching-web-sources` — for Phase 1 web research
- `writing-specifications` — if the carrier adapter needs a spec before implementation
