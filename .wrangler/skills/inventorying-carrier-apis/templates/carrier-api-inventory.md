# {Carrier Name} — API Inventory

> **Status**: Draft | In Review | Complete
> **Methodology**: Documentation review only — not verified with live API testing
> **Researched by**: {agent/person}
> **Date**: {YYYY-MM-DD}
> **Last verified**: {YYYY-MM-DD}

---

## Carrier Overview

| Detail | Value |
|--------|-------|
| **Carrier Name** | {Full legal name + common name} |
| **SCAC Code** | {Standard Carrier Alpha Code} |
| **Parent Company** | {If subsidiary, e.g. "Maersk" for Safmarine} |
| **Alliance** | {2M / THE Alliance / Ocean Alliance / None / Independent} |
| **Primary Services** | {Container, RoRo, Bulk, etc.} |
| **Market Position** | {e.g. "Largest global carrier" or "Regional specialist, West Africa/South America"} |
| **Headquarters** | {Country} |
| **Website** | {URL} |

---

## Digital Ecosystem & Developer Experience

| Detail | Value |
|--------|-------|
| **Has developer portal?** | Yes / No |
| **Developer portal URL** | {URL or "N/A"} |
| **API documentation URL(s)** | {URL(s) or "N/A"} |
| **Documentation quality** | Excellent / Good / Fair / Poor / None |
| **OpenAPI/Swagger spec available?** | Yes ({URL}) / No |
| **Sandbox/test environment?** | Yes ({URL or description}) / No / Unknown |
| **Developer registration** | Free self-service / Requires approval / Requires partnership / N/A |
| **Developer support channels** | {e.g. "developer@carrier.com, Slack community, GitHub issues"} |
| **Community/third-party libraries** | {Links to GitHub repos, npm/PyPI packages, or "None found"} |
| **API changelog / release notes** | {URL or "Not published"} |

---

## Standards Compliance

### DCSA (Digital Container Shipping Association)

| Detail | Value |
|--------|-------|
| **DCSA member?** | Yes / No / Unknown |
| **DCSA APIs implemented** | {List: TNT, BKG, CS, EDoc, or "None"} |
| **DCSA TNT version** | {e.g. "v3.0.0" or "N/A"} |
| **DCSA Booking version** | {e.g. "v2.0.4" or "N/A"} |
| **DCSA Schedules version** | {e.g. "v1.0.2" or "N/A"} |
| **Known DCSA deviations** | {Describe any known deviations from the standard, or "None known" or "N/A"} |

### Other Standards

| Standard | Supported? | Notes |
|----------|-----------|-------|
| **UN/EDIFACT (EDI)** | Yes / No / Unknown | {e.g. "IFTMIN, COPARN supported"} |
| **INTTRA integration** | Yes / No / Unknown | |
| **BIC (Bureau International des Containers)** | Yes / No / Unknown | {Container ID standards} |
| **UN/LOCODE** | Yes / No / Unknown | {Port code standard} |

---

## Authentication & Access

| Detail | Value |
|--------|-------|
| **Auth mechanism** | {OAuth2 / API Key / Bearer Token / Basic Auth / Client Certificate / None / N/A} |
| **Auth documentation** | {URL} |
| **Registration process** | {Step-by-step: how to get credentials} |
| **Registration URL** | {URL} |
| **Partner program required?** | {No / Yes — for production / Yes — for any access} |
| **Partner program URL** | {URL or "N/A"} |
| **Credential type** | {Client ID + Secret / API Key / Username + Password / etc.} |
| **Token refresh supported?** | {Yes / No / N/A} |
| **Token lifetime** | {e.g. "1 hour" or "N/A"} |
| **Access tiers** | {e.g. "Free (100 req/day), Partner (10k req/day), Enterprise (unlimited)" or "Single tier"} |
| **IP whitelisting required?** | {Yes / No / Unknown} |

### Access Requirements Summary for SSL

{1-2 paragraphs: what does SSL specifically need to do to get API access? Is it self-service, or do they need to contact the carrier's partnership team? What business documentation might be required?}

---

## Required Fields Coverage Matrix

> Cross-reference with `docs/required-data-fields.md`. Every field must have a row.
> ✅ = Available | ❌ = Not available | ⚠️ = Partial / limited | ❓ = Unknown

### API Domains (Section 1)

| # | Canonical Field | Available? | Carrier Endpoint | Carrier Field/Event Code | DCSA Mapping | Notes |
|---|----------------|-----------|-----------------|-------------------------|-------------|-------|
| 1.1 | Vessel & Port Schedules | | | | | |
| 1.2 | Point-to-Point Route Options | | | | | |
| 1.3 | Booking Lifecycle | | | | | |
| 1.4 | Shipping Instructions | | | | | |
| 1.5 | Rates | | | | | |

### Document & Cargo Cutoffs (Section 2)

| # | Canonical Field | Abbrev | Available? | Carrier Endpoint | Carrier Field/Event Code | DCSA Mapping | Notes |
|---|----------------|--------|-----------|-----------------|-------------------------|-------------|-------|
| 2.1 | Earliest Container Release Date | ERD | | | | `ECP` | |
| 2.2 | Earliest Full-Container Delivery Date | EFC | | | | `EFC` | |
| 2.3 | Documentation Cutoff | DCO | | | | `DCO` | |
| 2.4 | VGM Cutoff | VCO | | | | `VCO` | |
| 2.5 | Container/Cargo Cutoff (FCL) | FCO | | | | `FCO` | |
| 2.6 | Container/Cargo Cutoff (LCL) | LCO | | | | `LCO` | |

### Container Milestones (Section 3)

| # | Canonical Field | Available? | Carrier Endpoint | Carrier Event Signature | DCSA Mapping | Notes |
|---|----------------|-----------|-----------------|------------------------|-------------|-------|
| 3.1 | Container Assigned to Booking | | | | Inferred from first EquipmentEvent | |
| 3.2 | Empty Container Gate-Out (Pickup) | | | | `GTOT` + `EMPTY` | |
| 3.3 | Full Container Gate-In | | | | `GTIN` + `LADEN` | |
| 3.4 | Container Loaded on Vessel | | | | `LOAD` + `LADEN` | |
| 3.5 | Container Discharged at Transshipment | | | | `DISC` + `LADEN` + transshipment | |
| 3.6 | Container Loaded at Transshipment | | | | `LOAD` + `LADEN` + transshipment | |
| 3.7 | Container Discharged at Destination | | | | `DISC` + `LADEN` | |
| 3.8 | Full Container Gate-Out (Delivery) | | | | `GTOT` + `LADEN` | |
| 3.9 | Empty Container Returned | | | | `GTIN` + `EMPTY` | |

### Vessel Schedule / Transport Events (Section 4)

| # | Canonical Field | Abbrev | Available? | Carrier Endpoint | Carrier Event Signature | DCSA Mapping | Notes |
|---|----------------|--------|-----------|-----------------|------------------------|-------------|-------|
| 4.1 | Estimated Time of Departure | ETD | | | | `DEPA` + classifier | |
| 4.2 | Estimated Time of Arrival | ETA | | | | `ARRI` + classifier | |
| 4.3 | Vessel Arrival at Transshipment | — | | | | `ARRI` at intermediate | |
| 4.4 | Vessel Departure from Transshipment | — | | | | `DEPA` at intermediate | |

### Change Detection (Section 5)

| Mechanism | Supported? | Details |
|-----------|-----------|---------|
| Event Subscriptions (Webhooks) | | |
| Event Polling with timestamp filter | | |
| Event Classifier Progression (PLN→EST→ACT) | | |
| Retracted Events | | |
| Change Remarks | | |
| Delay Reason Codes | | |

### Coverage Summary

| Category | Fields Available | Fields Unavailable | Fields Unknown | Coverage % |
|----------|-----------------|-------------------|----------------|-----------|
| API Domains (1.x) | /5 | /5 | /5 | % |
| Cutoffs (2.x) | /6 | /6 | /6 | % |
| Container Milestones (3.x) | /9 | /9 | /9 | % |
| Transport Events (4.x) | /4 | /4 | /4 | % |
| Change Detection (5.x) | /6 | /6 | /6 | % |
| **Total** | **/30** | **/30** | **/30** | **%** |

---

## Endpoint Reference

<!--
  Document every relevant endpoint.
  For carriers with no API, delete this section entirely.
-->

### Tracking / Events

| Endpoint | Method | Lookup Params | Pagination | Response Format | Notes |
|----------|--------|--------------|------------|----------------|-------|
| `{path}` | GET | {B/L, Booking, Container} | {cursor/offset/none} | {JSON/XML} | |
| | | | | | |

### Schedules

| Endpoint | Method | Key Params | Pagination | Response Format | Notes |
|----------|--------|-----------|------------|----------------|-------|
| `{path}` | GET | {port, vessel, date range} | {cursor/offset/none} | {JSON/XML} | |
| | | | | | |

### Booking

| Endpoint | Method | Key Params | Pagination | Response Format | Notes |
|----------|--------|-----------|------------|----------------|-------|
| `{path}` | GET | {booking ref} | {cursor/offset/none} | {JSON/XML} | |
| | | | | | |

### Other Endpoints

| Endpoint | Method | Purpose | Key Params | Notes |
|----------|--------|---------|-----------|-------|
| `{path}` | {method} | {description} | | |
| | | | | |

### Lookup Methods Summary

| Identifier Type | Supported? | Which Endpoints? | Notes |
|----------------|-----------|-----------------|-------|
| **Bill of Lading (B/L) number** | | | |
| **Booking reference** | | | |
| **Container number** | | | |
| **Carrier booking number** | | | {if different from customer booking ref} |
| **Vessel IMO / voyage** | | | |

---

## Rate Limits & Quotas

| Detail | Value | Source |
|--------|-------|--------|
| **Global rate limit** | {e.g. "100 req/min" or "Unpublished"} | {Official/Reported/Estimated/Unknown} |
| **Per-endpoint limits** | {e.g. "Tracking: 50/min, Schedules: 20/min" or "Same as global"} | {source} |
| **Daily/monthly quota** | {e.g. "10,000 req/day" or "Unpublished"} | {source} |
| **Rate limit response** | {e.g. "429 with Retry-After header" or "Unknown"} | {source} |
| **Rate limit by** | {IP / API key / OAuth token / Unknown} | {source} |
| **Burst allowance** | {e.g. "10 req/sec burst" or "Unknown"} | {source} |
| **Different tiers** | {e.g. "Free: 100/day, Partner: 10k/day" or "Single tier"} | {source} |

---

## Webhook / Push Support

<!--
  Delete this section if carrier does not support webhooks/push.
-->

| Detail | Value |
|--------|-------|
| **Webhooks supported?** | Yes / No / Unknown |
| **Subscription endpoint** | {e.g. "POST /v3/event-subscriptions"} |
| **Subscription management** | {e.g. "Create/list/delete via API"} |
| **Subscribable event types** | {List which events can be subscribed to} |
| **Callback format** | {e.g. "DCSA event JSON POSTed to callbackUrl"} |
| **Callback authentication** | {e.g. "Shared secret in header" or "None"} |
| **Retry policy on failure** | {e.g. "3 retries with exponential backoff" or "Unknown"} |
| **Documented reliability** | {Any published SLA or known issues} |

---

## Non-API Access Methods

### Web Portal Tracking

| Detail | Value |
|--------|-------|
| **Tracking URL** | {e.g. "https://carrier.com/tracking"} |
| **Login required?** | Yes / No |
| **Data available on portal but NOT in API** | {List any data visible on portal that the API doesn't expose} |
| **Scraping feasibility** | {Feasible / Difficult (anti-bot) / Not assessed / ToS prohibits} |

### EDI (Electronic Data Interchange)

| Detail | Value |
|--------|-------|
| **EDI supported?** | Yes / No / Unknown |
| **EDI message types** | {e.g. "IFTMIN, COPARN, BAPLIE, IFTSTA"} |
| **EDI connection method** | {e.g. "AS2, SFTP, VAN"} |
| **EDI documentation** | {URL or "Contact carrier"} |

### Third-Party Aggregators

| Aggregator | Carrier Available? | Notes |
|-----------|-------------------|-------|
| **project44** | Yes / No / Unknown | |
| **FourKites** | Yes / No / Unknown | |
| **Vizion** | Yes / No / Unknown | |
| **INTTRA / E2open** | Yes / No / Unknown | |
| **CargoSmart** | Yes / No / Unknown | |
| **Other**: {name} | | |

> **Aggregator note**: If the carrier's own API is limited but they're available through an aggregator, note whether the aggregator exposes more data than the direct API. This may influence the SDK architecture (direct integration vs. aggregator integration).

---

## Data Quality & Operational Notes

| Aspect | Assessment | Source |
|--------|-----------|--------|
| **Event freshness** | {How quickly events appear in API after physical occurrence, e.g. "< 15 min" or "Unknown"} | {Official/Reported/Estimated/Unknown} |
| **API uptime/reliability** | {Any published SLA or known reliability info} | {source} |
| **Historical data depth** | {How far back can you query events? e.g. "90 days" or "Unknown"} | {source} |
| **Timezone handling** | {UTC / Local port time / Varies / Unknown} | {source} |
| **Character encoding** | {UTF-8 / Varies / Known issues} | {source} |

### Known Issues & Gotchas

<!-- List any known problems, quirks, or limitations that would affect SDK development -->

- {Gotcha 1}
- {Gotcha 2}

### Recently Deprecated or Changed

<!-- List any recently deprecated endpoints, fields, or API versions that might appear in older documentation -->

- {Deprecation 1}

---

## SDK Adapter Recommendations

### Feasibility Assessment

| Aspect | Assessment |
|--------|-----------|
| **Can we build a useful adapter?** | Yes / Partial / No — {explanation} |
| **Implementation effort** | Low (DCSA passthrough) / Medium (DCSA + deviations) / High (proprietary) / Very High (no API) |
| **DCSA base adapter usable?** | Yes / Yes with overrides / No — fully custom |

### Recommended Approach

{1-3 paragraphs: How should the SDK adapter for this carrier be built? What architectural approach? What's the primary access method?}

### Recommended Lookup Method

{Which shipment identifier type works best for this carrier's API? Why?}

### Recommended Polling Strategy

| Detail | Recommendation |
|--------|---------------|
| **Polling interval** | {e.g. "Every 2 hours" — based on rate limits and data freshness} |
| **Incremental filter** | {e.g. "Use eventCreatedDateTime > lastPollTime" or "Full re-fetch required"} |
| **Batch tracking** | {Can multiple shipments be tracked in one call? Or one call per shipment?} |

### Auth Implementation Notes

{Specific notes for implementing authentication in the adapter — OAuth flow details, token refresh patterns, credential handling}

### Edge Cases to Handle

- {Edge case 1: e.g. "Transshipment events may arrive out of order"}
- {Edge case 2: e.g. "Container number changes after initial booking"}
- {Edge case 3: e.g. "Rolled containers appear as cancellation + re-booking"}

### Mapping Complexity

{How complex is the mapping from this carrier's response format to our canonical data model? What are the tricky parts?}

---

## Open Questions

<!-- Things that couldn't be determined from desk research -->

- [ ] {Question 1 — e.g. "Does SSL have an existing business relationship with this carrier?"}
- [ ] {Question 2 — e.g. "What test data is available in the sandbox?"}
- [ ] {Question 3 — e.g. "Are rate limits enforced per-key or per-IP?"}

---

## Source Links

| Resource | URL |
|----------|-----|
| Carrier Website | {URL} |
| Developer Portal | {URL} |
| API Documentation | {URL} |
| OpenAPI/Swagger Spec | {URL} |
| Authentication Docs | {URL} |
| Rate Limit Docs | {URL} |
| Webhook Docs | {URL} |
| Changelog / Release Notes | {URL} |
| Community Library 1 | {URL} |
| Community Library 2 | {URL} |
| Forum / Discussion | {URL} |
| {Other relevant resource} | {URL} |
