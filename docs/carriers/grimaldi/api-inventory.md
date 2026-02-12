# Grimaldi Lines — API Inventory

> **Status**: Complete
> **Methodology**: Documentation review only — not verified with live API testing. Research based on public web presence, DCSA membership records, industry databases, third-party aggregator carrier lists, and community sources. No developer portal or API documentation was found to review.
> **Researched by**: Agent (carrier API audit workflow)
> **Date**: 2026-02-11
> **Last verified**: 2026-02-11

---

## Carrier Overview

| Detail | Value |
|--------|-------|
| **Carrier Name** | Grimaldi Lines (Grimaldi Group S.p.A.) |
| **SCAC Code** | GRIM |
| **Parent Company** | Grimaldi Group (family-owned, Naples, Italy) |
| **Alliance** | Independent (not a member of 2M, THE Alliance, or Ocean Alliance) |
| **Primary Services** | RoRo (Roll-on/Roll-off), container, car carriers, project cargo, passenger ferries |
| **Market Position** | World's largest operator of RoRo vessels; niche but important in Mediterranean, West Africa, South America, and North America trades. Not a top-10 container carrier by TEU volume but significant in specialized cargo |
| **Headquarters** | Naples, Italy |
| **Website** | [https://www.grimaldi-lines.com](https://www.grimaldi-lines.com) |

### Grimaldi Group Corporate Structure (Relevant Entities)

| Entity | SCAC | Role | Digital Infrastructure |
|--------|------|------|----------------------|
| **Grimaldi Lines** | GRIM | Deep-sea and short-sea RoRo/container services | Main tracking portal at grimaldi-lines.com |
| **Atlantic Container Line (ACL)** | ACLU | Transatlantic container/RoRo (US-Europe) | Separate portal at aclcargo.com |
| **Grimaldi Euromed** | — | Short-sea Mediterranean services | Likely shares Grimaldi Lines infrastructure |
| **Finnlines** | — | RoPax services (Baltic/North Sea) | Separate entity, separate digital platform |
| **Minoan Lines** | — | Ferry services (Greece) | Separate entity, passenger-focused |

> **Note on ACL**: Atlantic Container Line is a wholly-owned subsidiary of Grimaldi Group and operates independently with its own SCAC code (ACLU), its own website (aclcargo.com), and likely its own tracking systems. ACL specializes in transatlantic container and RoRo services between North America and Europe. Whether ACL and Grimaldi Lines share any backend digital infrastructure is unknown. ACL has a separate row in `docs/steamship-lines.md` and should receive its own audit if it is a priority for SSL. This inventory covers **Grimaldi Lines (GRIM)** specifically, with notes on ACL where relevant.

---

## Digital Ecosystem & Developer Experience

| Detail | Value |
|--------|-------|
| **Has developer portal?** | **No** |
| **Developer portal URL** | N/A |
| **API documentation URL(s)** | N/A — no public API documentation found |
| **Documentation quality** | None — no API documentation exists publicly |
| **OpenAPI/Swagger spec available?** | No |
| **Sandbox/test environment?** | No / N/A |
| **Developer registration** | N/A — no developer program exists |
| **Developer support channels** | N/A — general commercial contact only |
| **Community/third-party libraries** | None found — no GitHub repos, npm packages, or PyPI packages for Grimaldi API integration |
| **API changelog / release notes** | N/A |

### Research Performed

The following searches were conducted to confirm the absence of a public API:

1. **Direct website review**: grimaldi-lines.com — no developer, API, or integration section found. Site is primarily focused on commercial services, schedules, and passenger ferry booking.
2. **Search: "Grimaldi Lines API"** — no results for a developer API; results are about shipping services.
3. **Search: "Grimaldi SCAC GRIM API developer"** — no relevant results.
4. **Search: "Grimaldi Lines tracking API" / "Grimaldi Lines developer portal"** — no results.
5. **GitHub search: "grimaldi shipping" / "grimaldi lines" / "GRIM carrier"** — no repositories found for Grimaldi API integration.
6. **npm / PyPI search: "grimaldi"** — no shipping/tracking packages.
7. **Reddit / Stack Overflow search: "Grimaldi Lines API"** — no developer discussions.
8. **DCSA membership lists** — Grimaldi is not listed as a DCSA member.
9. **Open-source landscape memo** — confirms "Grimaldi | None | No known public API".

**Conclusion**: Grimaldi Lines does not offer a public REST/SOAP/GraphQL API for shipment tracking or any other logistics function. This is consistent with their market position as a specialist RoRo/container carrier that has historically relied on traditional B2B channels (EDI, email, portal) rather than digital API platforms.

---

## Standards Compliance

### DCSA (Digital Container Shipping Association)

| Detail | Value |
|--------|-------|
| **DCSA member?** | **No** — Grimaldi is not a DCSA member. The 10 founding/current members are: MSC, Maersk, CMA CGM, Hapag-Lloyd, ONE, Evergreen, Yang Ming, HMM, ZIM, and PIL. Grimaldi is not among them |
| **DCSA APIs implemented** | None |
| **DCSA TNT version** | N/A |
| **DCSA Booking version** | N/A |
| **DCSA Schedules version** | N/A |
| **Known DCSA deviations** | N/A — no DCSA implementation |

### Other Standards

| Standard | Supported? | Notes |
|----------|-----------|-------|
| **UN/EDIFACT (EDI)** | Likely Yes (unverified) | As a major international carrier, Grimaldi almost certainly supports standard EDI messages (IFTMIN, COPARN, BAPLIE, IFTSTA) for business partners. EDI is standard in ocean shipping and required for port terminal operations. However, no public documentation of their EDI capabilities was found — access likely requires a direct business relationship |
| **INTTRA integration** | Likely Yes (unverified) | INTTRA (now part of E2open) is used by most carriers for booking and documentation. Grimaldi likely participates in the INTTRA network given their operational scale, but this could not be confirmed from public sources |
| **BIC (Bureau International des Containers)** | Yes (inferred) | Grimaldi operates ISO containers and must comply with BIC container identification standards |
| **UN/LOCODE** | Yes (inferred) | Standard port codes used in all shipping operations — Grimaldi's schedules reference standard port codes |

---

## Authentication & Access

| Detail | Value |
|--------|-------|
| **Auth mechanism** | N/A — no API exists |
| **Auth documentation** | N/A |
| **Registration process** | N/A |
| **Registration URL** | N/A |
| **Partner program required?** | N/A for API; Yes for EDI access (business relationship required) |
| **Partner program URL** | N/A — contact commercial department directly |
| **Credential type** | N/A |
| **Token refresh supported?** | N/A |
| **Token lifetime** | N/A |
| **Access tiers** | N/A |
| **IP whitelisting required?** | N/A |

### Web Portal Access

| Detail | Value |
|--------|-------|
| **Portal login required for tracking?** | Likely — most carrier portals require registration for detailed tracking |
| **Portal registration** | Through grimaldi-lines.com contact/registration |
| **Portal credentials** | Username/password (standard web login) |

### Access Requirements Summary for SSL

Grimaldi Lines has **no public API**. To get programmatic access to Grimaldi shipment data, SSL has the following realistic options:

1. **Third-party aggregator**: Use a commercial tracking aggregator (Vizion, Terminal49, project44, etc.) that includes Grimaldi in their carrier network. This is the most practical path for programmatic access. The aggregator handles the carrier integration (likely via portal scraping, EDI, or private partnership).

2. **EDI partnership**: Establish a direct EDI connection with Grimaldi for IFTSTA (status) messages. This requires a business relationship, EDI infrastructure (AS2, SFTP, or VAN), and technical integration work. Most appropriate if SSL has significant Grimaldi volume.

3. **Web portal scraping**: Use the Grimaldi web tracking portal programmatically. This is fragile, may violate Terms of Service, and requires ongoing maintenance as the portal UI changes.

4. **Manual process**: Continue monitoring Grimaldi shipments manually through the web portal. Appropriate if Grimaldi volume is low.

**Recommendation**: Start by checking whether Grimaldi is available through SSL's preferred aggregator (see Third-Party Aggregators section below). If not, evaluate EDI based on shipment volume with Grimaldi.

---

## Required Fields Coverage Matrix

> Cross-reference with `docs/required-data-fields.md`. Every field must have a row.
> Since Grimaldi has **no public API**, all fields are assessed based on web portal visibility and likely EDI capability.
>
> Legend: ✅ = Available via some method | ❌ = Not available / confirmed absent | ⚠️ = Partial / limited | ❓ = Unknown — could not determine from public sources
>
> **Source column key**: Portal = web tracking portal | EDI = likely via EDI partnership | Aggregator = via third-party aggregator | None = no known source

### API Domains (Section 1)

| # | Canonical Field | Available? | Source | Notes |
|---|----------------|-----------|--------|-------|
| 1.1 | Vessel & Port Schedules | ⚠️ | Portal | Grimaldi publishes sailing schedules on their website. These are static/PDF-based schedule publications, not a queryable API. Schedule data would need to be scraped or manually extracted |
| 1.2 | Point-to-Point Route Options | ⚠️ | Portal | May be derivable from published schedules on the website, but no structured query interface exists |
| 1.3 | Booking Lifecycle | ❓ | EDI / Portal | Booking is likely handled via EDI (IFTMIN) for partners, or through the web portal. No API access |
| 1.4 | Shipping Instructions | ❓ | EDI / Portal | SI submission likely via EDI or portal for business partners |
| 1.5 | Rates | ❌ | None | No public rate API. Rates are provided via commercial negotiation (email/PDF quotes). This is standard for most carriers outside of Maersk's spot rate API |

### Document & Cargo Cutoffs (Section 2)

| # | Canonical Field | Abbrev | Available? | Source | DCSA Mapping | Notes |
|---|----------------|--------|-----------|--------|-------------|-------|
| 2.1 | Earliest Container Release Date | ERD | ❓ | Unknown | `ECP` | May be communicated via booking confirmation email/portal. Not available via API |
| 2.2 | Earliest Full-Container Delivery Date | EFC | ❓ | Unknown | `EFC` | Same as above — likely in booking confirmation if provided |
| 2.3 | Documentation Cutoff | DCO | ⚠️ | Portal / Email | `DCO` | Typically communicated in booking confirmation. May appear on portal schedule pages. Critical for operations but no programmatic access |
| 2.4 | VGM Cutoff | VCO | ⚠️ | Portal / Email | `VCO` | SOLAS requirement — Grimaldi must communicate this. Likely in booking confirmation documents |
| 2.5 | Container/Cargo Cutoff (FCL) | FCO | ⚠️ | Portal / Email | `FCO` | Terminal/port cutoff times typically published in schedule or booking confirmation |
| 2.6 | Container/Cargo Cutoff (LCL) | LCO | ❓ | Unknown | `LCO` | Grimaldi's LCL operations are limited given their RoRo focus. May not be applicable for most Grimaldi shipments |

### Container Milestones (Section 3)

| # | Canonical Field | Available? | Source | DCSA Mapping | Notes |
|---|----------------|-----------|--------|-------------|-------|
| 3.1 | Container Assigned to Booking | ❓ | Unknown | Inferred from first EquipmentEvent | May be in booking confirmation but unlikely available in structured data |
| 3.2 | Empty Container Gate-Out (Pickup) | ❓ | Portal / EDI | `GTOT` + `EMPTY` | Terminal-generated event. May appear on tracking portal if Grimaldi integrates terminal data |
| 3.3 | Full Container Gate-In | ⚠️ | Portal / EDI | `GTIN` + `LADEN` | Likely visible on tracking portal — terminal receipt is a standard tracked event |
| 3.4 | Container Loaded on Vessel | ⚠️ | Portal / EDI | `LOAD` + `LADEN` | Core tracking milestone — likely shown on web tracking portal |
| 3.5 | Container Discharged at Transshipment | ❓ | Portal / EDI | `DISC` + `LADEN` + transshipment | Grimaldi uses fewer transshipments than major container lines (more direct services), but when applicable, this may be tracked on portal |
| 3.6 | Container Loaded at Transshipment | ❓ | Portal / EDI | `LOAD` + `LADEN` + transshipment | Same as 3.5 — depends on transshipment visibility |
| 3.7 | Container Discharged at Destination | ⚠️ | Portal / EDI | `DISC` + `LADEN` | Core milestone — likely shown on web tracking portal |
| 3.8 | Full Container Gate-Out (Delivery) | ❓ | Portal / EDI | `GTOT` + `LADEN` | May appear on tracking portal if terminal integrates delivery data |
| 3.9 | Empty Container Returned | ❓ | Unknown | `GTIN` + `EMPTY` | Least commonly tracked milestone. Unlikely to be visible on basic tracking portals |

### Vessel Schedule / Transport Events (Section 4)

| # | Canonical Field | Abbrev | Available? | Source | DCSA Mapping | Notes |
|---|----------------|--------|-----------|--------|-------------|-------|
| 4.1 | Estimated Time of Departure | ETD | ⚠️ | Portal / Schedule | `DEPA` + classifier | Published in sailing schedules on website. May update on tracking portal. No progression tracking (PLN→EST→ACT) available via API |
| 4.2 | Estimated Time of Arrival | ETA | ⚠️ | Portal / Schedule | `ARRI` + classifier | Same as ETD — published schedules show planned ETA. Updated ETAs may appear on tracking portal |
| 4.3 | Vessel Arrival at Transshipment | — | ❓ | Unknown | `ARRI` at intermediate | Grimaldi's direct service model means fewer transshipments, but when applicable, portal may show intermediate port calls |
| 4.4 | Vessel Departure from Transshipment | — | ❓ | Unknown | `DEPA` at intermediate | Same as 4.3 |

### Change Detection (Section 5)

| Mechanism | Supported? | Details |
|-----------|-----------|---------|
| Event Subscriptions (Webhooks) | ❌ | No API = no webhook support |
| Event Polling with timestamp filter | ❌ | No API = no polling support |
| Event Classifier Progression (PLN→EST→ACT) | ❌ | No structured event model available via API |
| Retracted Events | ❌ | N/A |
| Change Remarks | ❌ | N/A |
| Delay Reason Codes | ❌ | N/A |

### Coverage Summary

| Category | Fields Available | Fields Unavailable | Fields Unknown | Coverage % |
|----------|-----------------|-------------------|----------------|-----------|
| API Domains (1.x) | 0/5 | 1/5 | 4/5 | 0% (API), ~40% (portal/partial) |
| Cutoffs (2.x) | 0/6 | 0/6 | 6/6 | 0% (API), ❓ (portal) |
| Container Milestones (3.x) | 0/9 | 0/9 | 9/9 | 0% (API), ❓ (portal) |
| Transport Events (4.x) | 0/4 | 0/4 | 4/4 | 0% (API), ~50% (portal/partial) |
| Change Detection (5.x) | 0/6 | 6/6 | 0/6 | 0% |
| **Total** | **0/30** | **7/30** | **23/30** | **0% (API)** |

> **Important**: The 0% API coverage reflects the absence of a programmatic API. The web tracking portal and EDI likely provide access to a subset of these fields (estimated 30–50% for basic container milestones and vessel schedules), but this could not be verified without portal login access. The ❓ fields indicate data that is likely available through the web portal or EDI but could not be confirmed from public documentation.

---

<!-- Endpoint Reference section deleted — carrier has no API -->

## Non-API Access Methods

### Web Portal Tracking

| Detail | Value |
|--------|-------|
| **Tracking URL** | [https://www.grimaldi-lines.com](https://www.grimaldi-lines.com) — cargo tracking section (exact path could not be verified without login) |
| **Login required?** | Likely Yes for detailed container tracking; schedule information may be publicly visible |
| **Data available on portal** | Expected: vessel schedules, basic container status (loaded/discharged/delivered), ETD/ETA for sailings. Exact field coverage unknown |
| **Scraping feasibility** | Not assessed — would require portal access to evaluate. Considerations: anti-bot measures unknown, Terms of Service likely prohibit automated access, portal may use dynamic JavaScript rendering (harder to scrape), updates may be infrequent for niche carrier |
| **Portal technology** | Unknown — could not assess without access |

### ACL (Atlantic Container Line) Tracking

| Detail | Value |
|--------|-------|
| **Tracking URL** | [https://www.aclcargo.com](https://www.aclcargo.com) — ACL has its own cargo tracking portal |
| **Login required?** | Likely Yes for detailed tracking |
| **Relationship to Grimaldi** | ACL is a wholly-owned Grimaldi Group subsidiary but operates independently with SCAC code ACLU. ACL focuses on transatlantic container/RoRo services (North America ↔ Europe). Whether ACL shares any backend tracking infrastructure with Grimaldi Lines is unknown |
| **Separate audit needed?** | Yes — if SSL ships via ACL, a dedicated audit of ACL's digital capabilities is recommended. ACL may have different (potentially better) tracking capabilities than Grimaldi's Mediterranean/Africa/South America operations |

### EDI (Electronic Data Interchange)

| Detail | Value |
|--------|-------|
| **EDI supported?** | Likely Yes (unverified) — EDI is standard for carriers of Grimaldi's scale |
| **Expected EDI message types** | IFTMIN (booking), COPARN (container release), BAPLIE (bay plan), IFTSTA (shipment status), COPINO (container pre-notification) |
| **EDI connection method** | Unknown — likely AS2 or SFTP; possibly via VAN (Value Added Network) |
| **EDI documentation** | Not publicly available — would require contacting Grimaldi's IT/commercial department |
| **EDI for tracking (IFTSTA)** | If Grimaldi supports IFTSTA messages, this would be the most reliable method for programmatic tracking data without a REST API. IFTSTA provides structured shipment status updates including container milestones and vessel movements. **However, this requires establishing an EDI partnership** |

### Third-Party Aggregators

| Aggregator | Carrier Available? | Confidence | Notes |
|-----------|-------------------|------------|-------|
| **project44** | ❓ Unknown | Low | project44 claims "all ocean carriers" but their actual coverage of smaller carriers like Grimaldi is uncertain. project44's enterprise pricing (~$3/container, $6,250+/mo minimum) makes it impractical to verify without a sales engagement |
| **FourKites** | ❓ Unknown | Low | FourKites focuses more on trucking; ocean coverage for niche carriers like Grimaldi is uncertain |
| **Vizion** | Likely Yes | Medium | Vizion claims 99% of ocean freight coverage and supports 18+ ocean carriers. Given their broad coverage claims and push-based approach (they likely scrape or have partnerships), Grimaldi may be included. Vizion pricing: $5/container, 15 free containers/month for testing |
| **INTTRA / E2open** | Likely Yes | Medium | INTTRA is the largest ocean shipping B2B platform with 70+ carrier connections. Given Grimaldi's global operations, they likely participate in INTTRA for booking and documentation. INTTRA was acquired by E2open in 2020 |
| **CargoSmart** | ❓ Unknown | Low | CargoSmart (OOCL subsidiary) focuses on major container carriers; Grimaldi as a RoRo specialist may not be covered |
| **Terminal49** | ❓ Unknown | Medium | Terminal49 claims 98% global carrier coverage. May include Grimaldi. Has a developer-friendly free tier (100 containers) suitable for verification |
| **Shipsgo** | Likely Yes | Medium | Shipsgo supports 160+ carriers and uses portal scraping/data partnerships. Broader carrier coverage makes Grimaldi inclusion more likely |
| **SeaRates** | ❓ Unknown | Low | Claims 97% worldwide coverage but verification would require testing |

> **Aggregator verification recommendation**: The most practical path to verify Grimaldi aggregator availability is to test with **Vizion** (15 free containers/month) or **Terminal49** (100 free containers on dev plan). Both have self-service signup and would quickly confirm whether Grimaldi tracking data is accessible through their platform.

### Email Notifications

| Detail | Value |
|--------|-------|
| **Email notifications sent?** | Likely — most carriers send basic booking confirmation and key milestone notifications via email |
| **Notification format** | Unknown — likely free-text email, not structured/machine-readable |
| **Events notified** | Unknown — likely booking confirmation, vessel departure, vessel arrival at minimum |
| **Useful for automation?** | Limited — email parsing is fragile and unreliable for systematic tracking |

---

## Data Quality & Operational Notes

| Aspect | Assessment | Source |
|--------|-----------|--------|
| **Event freshness** | Unknown — no API to assess | N/A |
| **API uptime/reliability** | N/A — no API | N/A |
| **Historical data depth** | Unknown | N/A |
| **Timezone handling** | Unknown | N/A |
| **Character encoding** | Unknown | N/A |

### Known Issues & Gotchas

- **No public API exists** — this is the fundamental constraint. All programmatic access must go through third-party aggregators, EDI, or portal scraping.
- **RoRo vs. container tracking differences** — Grimaldi's primary business is RoRo (vehicles, rolling cargo, project cargo), not containerized freight. Tracking data granularity may be different for RoRo cargo vs. containers. Standard container tracking milestones (gate-in, loaded, discharged, gate-out) may not map cleanly to RoRo cargo movements.
- **Geographic focus** — Grimaldi's primary routes are Mediterranean short-sea, Mediterranean–West Africa, Mediterranean–South America, and Mediterranean–North America. Their tracking infrastructure may vary by trade lane (e.g., European short-sea tracking may be more mature than West Africa deep-sea).
- **ACL confusion** — The Grimaldi Group includes ACL (SCAC: ACLU), which operates independently. SSL must clarify whether they ship with "Grimaldi Lines" (GRIM), "ACL" (ACLU), or both — they have separate tracking systems and potentially different digital capabilities.
- **Language/localization** — Grimaldi is an Italian company; some portal content and documentation may be primarily in Italian.
- **Limited digitalization** — As a family-owned, privately-held company focused on RoRo operations, Grimaldi has historically invested less in digital/API infrastructure compared to the major container-focused carriers (Maersk, MSC, CMA CGM, etc.) that are driven by competitive digital transformation and DCSA compliance.

### Recently Deprecated or Changed

No API changes to track — no API exists.

---

## Rate Limits & Quotas

N/A — no API exists.

---

<!-- Webhook / Push Support section deleted — carrier has no API/webhook support -->

---

## SDK Adapter Recommendations

### Feasibility Assessment

| Aspect | Assessment |
|--------|-----------|
| **Can we build a useful direct adapter?** | **No** — no public API exists. A direct Grimaldi adapter is not feasible without portal scraping or EDI integration, both of which are outside the current SDK scope |
| **Implementation effort** | **Very High** — no API means any adapter would require portal scraping (fragile, high maintenance) or EDI integration (significant infrastructure, business relationship required) |
| **DCSA base adapter usable?** | **No** — Grimaldi is not a DCSA member and has no DCSA-compliant API |

### Recommended Approach

**Primary recommendation: Aggregator integration**

The most practical path to programmatic Grimaldi tracking data is through a third-party aggregator. The SDK should support an "aggregator adapter" pattern where carriers without their own API are accessed through a commercial aggregator (Vizion, Terminal49, Shipsgo, etc.). This is a common pattern — even large enterprises use aggregators for carrier coverage gaps.

**Proposed architecture for API-less carriers:**

```
SDK CarrierAdapter (Grimaldi)
    │
    ├── Direct API → ❌ Not available
    │
    └── Aggregator Fallback
        ├── Vizion API → normalize aggregator response to canonical model
        ├── Terminal49 API → normalize aggregator response to canonical model
        └── Other aggregator → normalize aggregator response to canonical model
```

The aggregator adapter would:
1. Accept credentials for the aggregator (not the carrier)
2. Call the aggregator's tracking API with the Grimaldi shipment identifier
3. Normalize the aggregator's response to the SDK's canonical data model
4. Return the same `ShipmentState` structure as a direct carrier adapter

**Alternative approaches (if aggregator coverage is insufficient):**

1. **EDI integration** — If SSL has an existing EDI connection with Grimaldi, the SDK could include an EDI message parser that converts IFTSTA messages into the canonical data model. This would be an "ingest" adapter rather than a "fetch" adapter (push-based, not pull-based).

2. **Mark as unsupported with metadata** — The SDK's `getCarrierCapabilities('GRIM')` should return a capabilities object indicating no direct API support, with guidance on alternative access methods. This lets consumers make informed decisions.

3. **Web scraping** (not recommended for v1) — Portal scraping is technically possible but fragile, may violate ToS, requires ongoing maintenance, and is unsuitable for a production SDK library.

### Recommended Lookup Method

If accessed through an aggregator: **Bill of Lading (B/L) number** is the most universal identifier that aggregators support across carriers, followed by container number.

If accessed through EDI: **Booking reference** is the primary identifier used in EDI message flows.

### Recommended Polling Strategy

| Detail | Recommendation |
|--------|---------------|
| **Polling interval** | N/A for direct API. If using aggregator: follow aggregator's recommended polling interval (typically every 2-6 hours) |
| **Incremental filter** | Depends on aggregator capabilities |
| **Batch tracking** | Depends on aggregator — most support single-shipment lookups |

### Auth Implementation Notes

No direct authentication with Grimaldi is required (or possible). If using an aggregator:
- **Vizion**: API key authentication
- **Terminal49**: API key authentication
- **project44**: OAuth2 client credentials flow

The SDK's credential model should support "aggregator credentials" as a first-class concept for carriers without direct API access.

### Edge Cases to Handle

- **RoRo cargo tracking**: Grimaldi ships significant RoRo cargo (vehicles, heavy equipment, rolling stock). Tracking milestones for RoRo cargo differ from containerized freight — there may be no "container number" identifier, and milestones like "gate-in" and "gate-out" may map to vehicle loading/unloading ramps rather than container yard operations. The SDK should handle this gracefully (e.g., return available milestones without requiring container-specific events).
- **Mixed container/RoRo vessels**: Grimaldi operates ConRo vessels that carry both containers and RoRo cargo on the same voyage. A single vessel lookup might return both cargo types.
- **ACL vs. Grimaldi routing**: A booking may be advertised as Grimaldi but use ACL vessels on transatlantic legs (or vice versa). The tracking infrastructure and identifier systems may differ between the two entities.
- **Limited transshipment data**: Grimaldi operates more direct services than hub-and-spoke carriers, so transshipment events (3.5, 3.6) may be less common but also less visible when they do occur.
- **West Africa port infrastructure**: Some Grimaldi destination ports in West Africa may have limited terminal digitalization, leading to delayed or missing terminal events (gate-in, gate-out).

### Mapping Complexity

**N/A for direct integration** — no API response to map.

**For aggregator integration**: Mapping complexity depends entirely on which aggregator is used. Most aggregators (Vizion, Terminal49) already normalize carrier data into their own event model, which would need a second normalization layer to the SDK's canonical model. This is **medium complexity** — the aggregator does the hard work of carrier integration, and the SDK maps from a well-documented aggregator response format to the canonical model.

---

## Open Questions

- [ ] **Q1: Does SSL currently ship with Grimaldi Lines?** — Need to confirm actual volume to determine investment priority. Is it Grimaldi Lines (GRIM), ACL (ACLU), or both?
- [ ] **Q2: Does SSL have an existing EDI connection with Grimaldi?** — If yes, IFTSTA messages could be a data source for tracking. The SDK could include an EDI message parser.
- [ ] **Q3: Which aggregator does SSL prefer/already use?** — If SSL has an existing aggregator relationship (project44, Vizion, etc.), we should prioritize that aggregator's API for the Grimaldi adapter.
- [ ] **Q4: Is Grimaldi available through SSL's preferred aggregator?** — This is the critical question. A quick test with Vizion (15 free containers/month) or Terminal49 (100 free containers) using a real Grimaldi B/L number would answer this definitively.
- [ ] **Q5: What cargo types does SSL ship via Grimaldi?** — Containers, RoRo vehicles, project cargo? This affects which milestones are relevant and how tracking identifiers work.
- [ ] **Q6: ACL audit needed?** — If SSL uses ACL (transatlantic), ACL should receive its own separate audit. ACL may have different (potentially better) digital capabilities than Grimaldi's core operations.
- [ ] **Q7: Grimaldi web portal access** — Can someone at SSL log into the Grimaldi cargo tracking portal and document what data is actually shown? This would resolve many ❓ fields in the coverage matrix.
- [ ] **Q8: Grimaldi commercial contact** — Has SSL inquired directly with Grimaldi about digital integration options? Some carriers offer private API access to business partners that isn't publicly advertised.

---

## Source Links

| Resource | URL | Status |
|----------|-----|--------|
| Grimaldi Lines Website | [https://www.grimaldi-lines.com](https://www.grimaldi-lines.com) | Active — commercial website, no developer section |
| ACL Cargo Website | [https://www.aclcargo.com](https://www.aclcargo.com) | Active — ACL subsidiary, separate tracking portal |
| Grimaldi Group Corporate | [https://www.grimaldi.napoli.it](https://www.grimaldi.napoli.it) | Active — corporate/group website |
| DCSA Member List | [https://dcsa.org/about/](https://dcsa.org/about/) | Grimaldi is NOT listed as a member |
| Open-Source Landscape Memo | `/.wrangler/memos/open-source-landscape-research.md` | Confirms: "Grimaldi \| None \| No known public API" |
| Vizion API (potential aggregator) | [https://www.vizionapi.com](https://www.vizionapi.com) | 99% ocean freight coverage claimed; Grimaldi inclusion unverified |
| Terminal49 (potential aggregator) | [https://www.terminal49.com](https://www.terminal49.com) | 98% global coverage claimed; Grimaldi inclusion unverified |
| INTTRA / E2open | [https://www.e2open.com](https://www.e2open.com) | Major ocean shipping platform; Grimaldi likely connected |

---

## Appendix: Grimaldi Group Fleet & Services Overview

Understanding Grimaldi's business helps contextualize why their digital infrastructure differs from major container carriers.

### Fleet Composition
- ~130 vessels (one of the largest privately-owned fleets)
- Primarily RoRo and ConRo (container + RoRo) vessels
- Also operates pure car/truck carriers (PCTC)
- Some pure container vessels

### Key Trade Routes
| Route | Service Type | Notes |
|-------|-------------|-------|
| Mediterranean short-sea | RoRo, container | High frequency, numerous ports |
| Mediterranean – West Africa | RoRo, container | Key market for Grimaldi |
| Mediterranean – South America (East Coast) | RoRo, container | Brazil, Argentina, Uruguay |
| Mediterranean – North America | RoRo, container | Often via ACL for transatlantic |
| Northern Europe – West Africa | RoRo, container | Via Mediterranean hub |
| Northern Europe – South America | RoRo, container | |

### Why Grimaldi's Digital Infrastructure Differs
1. **Private, family-owned company** — Less investor pressure for digital transformation compared to publicly-traded carriers
2. **RoRo specialist** — The RoRo logistics chain has different digitalization needs than containerized freight; tracking a rolling vehicle differs from tracking a TEU
3. **Not in DCSA** — No standards body pressure to implement specific APIs
4. **Niche market position** — Grimaldi competes on service quality and route specialization rather than digital tools
5. **B2B focus** — Most Grimaldi customers are large automotive manufacturers, OEMs, and established freight forwarders who use EDI or direct commercial relationships
