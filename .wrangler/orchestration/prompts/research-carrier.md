---
name: research-carrier
description: Research all API capabilities and access methods for an ocean carrier
---

Research all programmatic access methods for **{{ carrier.name }}** (SCAC: {{ carrier.scac }}).

## Reference Documents

Read these before starting:
- `docs/required-data-fields.md` -- the canonical list of fields SSL needs. Every field in this document must be addressed in your research.
- `docs/steamship-lines.md` -- carrier index for context on this carrier's known details.

## Research Areas

### 1. Developer Portal & API Documentation

- Find the carrier's developer portal (if it exists)
- Find all API documentation -- official docs, Swagger/OpenAPI specs, developer guides
- Note documentation quality: comprehensive, outdated, sparse, or absent
- Check if the carrier publishes an OpenAPI/Swagger specification file
- Note whether the developer portal is publicly accessible or requires registration to view docs

### 2. DCSA Compliance

- Determine whether the carrier is a DCSA member
- Identify which DCSA APIs they implement: Track & Trace (TNT), Booking (BKG), Commercial Schedules (CS), eDocumentation (EDoc)
- Note the DCSA version(s) supported
- Search for published information about DCSA deviations or extensions
- Check the DCSA website and GitHub repos (`github.com/dcsaorg`) for carrier-specific adoption notes

### 3. Authentication & Access Requirements

- What auth mechanism does the API use? (OAuth2, API key, bearer token, basic auth, client certificates)
- Is developer registration free and self-service, or does it require a business relationship / partner agreement?
- Does the carrier distinguish between test/sandbox and production access?
- Are there different access tiers (free, partner, enterprise)?
- What credentials are needed? (client ID/secret, API key, username/password)

### 4. Tracking / Shipment Visibility Endpoints

Find ALL endpoints related to shipment tracking, container tracking, vessel tracking. For each endpoint, document: HTTP method, path, supported lookup parameters (B/L number, booking reference, container number, vessel IMO).

Search beyond the obvious tracking endpoints -- some carriers expose separate endpoints for:
- Vessel schedules, port schedules
- Booking status, shipping instruction status
- Container events, transport events (vessel movements)
- Cutoff times, route/routing information

**Endpoint discovery checklist** (don't stop at the main docs page):
- Browse the full API reference listing, not just "Getting Started" guides
- If the carrier has an OpenAPI spec, parse it for all paths -- look for paths containing: `/track`, `/event`, `/shipment`, `/booking`, `/schedule`, `/vessel`, `/container`, `/transport`
- Check community client libraries -- their endpoint/method lists often include endpoints the main docs don't highlight
- Search for recently added endpoints in changelogs, developer blogs, release notes
- Look for variant endpoints -- some carriers have parallel endpoints for different lookup methods
- Check for versioned APIs with newer versions exposing additional data

### 5. Alternative / Non-API Access Methods

- Web portal for manual tracking? Document the URL and whether login is required
- EDI support? (IFTMIN, COPARN, BAPLIE, IFTSTA)
- Third-party aggregator availability -- check each: **project44**, **FourKites**, **Vizion**, **INTTRA/E2open**, **CargoSmart**
- Email notifications? Structured or free-text?
- Data export / download functionality?

### 6. Community & Third-Party Resources

- GitHub libraries or API wrappers for this carrier
- npm, PyPI published packages
- Forums, Reddit, Stack Overflow developer experiences
- Blog posts, conference talks, case studies about integrating with this carrier
- Industry data-sharing initiatives beyond DCSA

## Research Tips

- Search `site:reddit.com "{carrier name} API"` for developer experiences
- Search `"{carrier name}" tracking API developer` for integration guides
- Search `"{carrier name}" DCSA` for standards compliance info
- Check `{carrier name} API changelog` or `{carrier name} developer blog` for recent changes
- Search for the carrier's SCAC code -- some API docs reference carriers by SCAC rather than name
- If the carrier is part of an alliance (2M, THE Alliance, Ocean Alliance), check if the alliance has shared API infrastructure

## Sourcing Requirements

Every quantitative claim (rate limits, response times, data freshness, event delay) must carry a confidence tier:

| Tier | Label | Use when |
|------|-------|----------|
| **Official** | State as fact | Published in carrier's own docs with a linkable URL |
| **Reported** | "Reported as ~X" | From developer forums, Reddit, community testing; include source |
| **Estimated** | "Estimated ~X (unverified)" | Your inference from indirect evidence; no primary source |
| **Unknown** | Use "Unknown" | No evidence at all |

Rules:
- If the carrier publishes no official numbers, say "Unpublished" -- do NOT invent a specific number
- If you cannot find the specific page that states a fact, do not attribute it to "the carrier's docs"
- When simplifying complex systems, state the exceptions
- Deprecation claims must be precise with links to the notice
- Version claims must specify which version (don't say "supports DCSA TNT" without the version number)

## If the Carrier Appears to Have No API

Search thoroughly before concluding -- some carriers hide their APIs behind partner portals or alliance infrastructure. If you genuinely find no public API:

- **Confirm the absence** -- check partner portals, alliance shared infrastructure, and aggregator platforms
- **Document the web portal tracking** -- URL, what data it shows, whether it requires login
- **Check third-party aggregators** -- the carrier may be accessible through project44, FourKites, Vizion, INTTRA, or CargoSmart even without their own API
- **Check for EDI** -- some carriers support EDI messaging (IFTMIN, COPARN, BAPLIE, IFTSTA) even without REST/SOAP APIs
- **Assess scraping feasibility** -- is the portal scrapable? Does it use anti-bot measures? What are the ToS?
- **Document the gap clearly** -- so the SDK team can make an informed decision about whether and how to support this carrier

## Output

Write your findings to `docs/carriers/{{ carrier.slug }}/research-notes.md` as a structured document with clearly labeled sections for each research area above. Include all source URLs inline. This document is the intermediate artifact that the next workflow step will use to fill the inventory template.

```json
{
  "carrierName": "Full carrier name",
  "scac": "SCAC code",
  "researchNotesPath": "docs/carriers/{slug}/research-notes.md",
  "hasApi": true,
  "isDcsaCompliant": true,
  "dcsaVersion": "v2.2",
  "estimatedFieldCoverage": "high/medium/low",
  "keyFindings": ["finding 1", "finding 2"],
  "blockers": ["any issues that prevent completing the inventory"]
}
```
