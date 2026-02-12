# Hapag-Lloyd API Inventory — Review Findings

**Reviewer**: Automated audit (research agent)
**Date**: 2026-02-11
**Document reviewed**: `docs/carriers/hapag-lloyd/api-inventory.md`
**Method**: Web research cross-referencing live API portal, OpenAPI spec, GitHub repos, PyPI, Maven Central, Wikipedia, and DCSA website.

---

## Verified Accurate

### Developer Portal & Registration
- ✅ **api-portal.hlag.com exists** — returns HTTP 200, loads a working Vue.js SPA
- ✅ **API catalog endpoint** (`/v1/apis`) returns valid JSON with all listed APIs
- ✅ **Registration described as free/self-service** — API description states: "This API is publicly available to all customers and registered customers on the API Portal. To get started, please register on the Hapag-Lloyd API Developer Portal..."
- ✅ **Portal terms and privacy pages** load correctly (HTTP 200)

### Authentication
- ✅ **IBM API Connect auth confirmed** — OpenAPI spec `securitySchemes` defines:
  - `"Client ID Header"`: `X-IBM-Client-Id` (apiKey, in: header)
  - `"Client Secret Header"`: `X-IBM-Client-Secret` (apiKey, in: header)
- ✅ Top-level security requires both headers on all endpoints
- ✅ Python SDK confirms pattern: `HapagLloydClient(client_id='xxx', client_secret='xxx')`

### BETA Status
- ✅ **TNT API BETA confirmed** — API description opens with: *"Please note that the Track & Trace API is currently available only as a BETA version. The solution is still in development and will undergo changes and improvements."*
- ✅ **v2.2.2 Pilot Test May 2025** confirmed in changelog within API description

### Rate Limits
- ✅ **All three tiers exactly match** the official API documentation:
  - Tryout: 100 calls/day, 10 calls/min ✓
  - Default: 6,000 calls/day, 10 calls/min ✓
  - Premium: 100,000 calls/day, 150 calls/min ✓

### Mock Endpoint
- ✅ **mock.api-portal.hlag.com exists** — listed as a server in the OpenAPI spec
- ✅ Returns HTTP 200 with mock event data when proper auth headers are included
- ✅ Returns HTTP 400 with clear error message when auth headers are missing (validates header presence but accepts any value)

### API Catalog & Versions
- ✅ **All API names and versions match** the live catalog:
  - DCSA Track & Trace API specification v2.2.4 ✓
  - DCSA Commercial Schedules API v1.0.1 ✓
  - DCSA OVS v3.0.7 ✓
  - Prices API v2.1.2 ✓
  - Authentication v1.0.37 ✓
  - Transportation Status v1.1.8 ✓
  - Inland Delays v2.1.8 ✓
  - Smart PTI v1.0.2 ✓
  - HL LIVE Subscription v3.0.7 ✓
  - Live Position Subscription v1.0.6 ✓
  - Live Position Events (2 entries, version N/A) ✓

### Event Codes & Coverage Matrix (spot-checked against OpenAPI spec)
- ✅ `shipmentEventTypeCode`: ISSU, CONF — exact match
- ✅ `documentTypeCode`: BKG, TRD — exact match
- ✅ `equipmentEventTypeCode`: LOAD, DISC, GTIN, GTOT, STUF, STRP — exact match
- ✅ `emptyIndicatorCode`: EMPTY, LADEN — exact match
- ✅ `transportEventTypeCode`: ARRI, DEPA — exact match
- ✅ `eventClassifierCode`: ACT, PLN, EST for transport/equipment events; ACT only for shipment events — matches inventory claims about PLN→EST→ACT progression
- ✅ `facilityTypeCodeTRN` enums present: BOCR, CLOC, COFS, COYA, OFFD, DEPO, INTE, POTE, RAMP
- ✅ `modeOfTransport`: VESSEL, RAIL, TRUCK, BARGE

### Query Parameters (verified against OpenAPI spec paths)
- ✅ All documented query params confirmed: `eventType`, `shipmentEventTypeCode`, `documentTypeCode`, `carrierBookingReference`, `transportDocumentReference`, `transportEventTypeCode`, `exportVoyageNumber`, `UNLocationCode`, `equipmentEventTypeCode`, `equipmentReference`, `eventCreatedDateTime`, `limit`, `sort`
- ✅ `vesselIMONumber` is **NOT** a query parameter (correctly noted in inventory) but IS present in response schema (`vessel` object)
- ✅ `carrierServiceCode` has 0 occurrences in the OpenAPI spec (correctly flagged as an open question)

### Webhook / Push Support
- ✅ **No webhooks for TNT confirmed** — FAQ verbatim: *"Do you offer a notification service for the DCSA Track & Trace events that pushes data to my system?" → "Not yet, but please observe the release notes for changes."*
- ✅ Rate Sheet push notification callback spec exists at GitHub with correct headers (`X-API-Key`, `X-Request-ID`, `X-Idempotency-Key`)

### DCSA Membership
- ✅ **Hapag-Lloyd is a DCSA founding member** — DCSA homepage confirms: *"In April 2019, MSC, Maersk, CMA CGM, Hapag-Lloyd, ONE, Evergreen, Yang Ming, HMM, and ZIM founded DCSA"*
- ✅ Founding date April 2019 confirmed
- ✅ DCSA JSON-LD data: `"foundingDate":"2019"`

### Deprecation & Known Issues
- ✅ `transportCallID` deprecation confirmed in OpenAPI spec: *"Deprecated - not needed as the TransportCall object is included"* (on both baseTransportEvent and baseEquipmentEvent)
- ✅ No `retractedEventID`, `changeRemark`, `delayReasonCode`, or `isTransshipmentMove` found anywhere in the spec
- ✅ Data discrepancy FAQ confirmed verbatim: *"We're including additional data sources for the API to better reflect the changes and align with the website view. We encourage you to trust the website data until our next release"*

### Community Libraries
- ✅ **Python `hapag-lloyd-sdk`** exists on PyPI: v0.2.1, author Aaron Frank (gedankenfabrik)
- ✅ **Ruby `hapag-lloyd_client`** exists on GitHub (Cellpap/hapag-lloyd_client), language: Ruby, auto-generated from DCSA TNT OpenAPI spec
- ✅ **GitHub `Hapag-Lloyd/customer-api-definitions`** exists with rate sheet OpenAPI spec

### Market Position
- ✅ **5th largest** confirmed by Wikipedia: "Hapag-Lloyd AG is a German international shipping and container transportation company, the 5th-largest in the world by total TEU capacity."

### Coverage Math
- ✅ **57% confirmed coverage math checks out**: 17/30 = 56.7% ≈ 57%
  - 1.x: 3, 2.x: 0, 3.x: 8, 4.x: 4, 5.x: 2 → total 17
- ✅ **Unavailable: 1/30** (webhooks only) ✓
- ✅ **Unknown: 9/30** (6 cutoffs + 3 change detection) ✓
- ✅ **Optimistic range reasonable**: If all unknowns available = 87%; if just cutoffs = 77%. Stated "75-85%" is a reasonable estimate.

### Server URLs
- ✅ TNT base URL `https://api.hlag.com/hlag/external/v2/events` confirmed in OpenAPI spec servers[0]
- ✅ Mock URL `https://mock.api-portal.hlag.com/v2/events` confirmed in OpenAPI spec servers[1]
- ✅ TNT base URL returns HTTP 405 (Method Not Allowed) when hit without params/auth — confirms server is live

---

## Issues Found

### 1. 🔴 Alliance Membership is WRONG (Outdated)
**Claim**: "THE Alliance (with ONE, HMM, Yang Ming)"
**Reality**: Hapag-Lloyd **left THE Alliance** in January 2025 and joined the **Gemini Cooperation** with Maersk, which launched operations in February 2025.

**Source**: Wikipedia (Hapag-Lloyd article, retrieved 2026-02-11): *"Hapag-Lloyd simultaneously announced that it would leave 'THE Alliance' in January 2025"* and *"Today, Hapag-Lloyd AG and the Danish Maersk A/S are partners in the Gemini cooperation. It launched operations in February 2025."*

**Impact**: This is a factual error in the Carrier Overview table. As of the document date (Feb 2026), Hapag-Lloyd has been in the Gemini Cooperation for a full year.

### 2. 🟡 Python SDK GitHub Repository is 404
**Claim**: Links to `https://github.com/gedankenfabrik/hapag_lloyd_sdk`
**Reality**: This URL returns HTTP 404 — the repository is deleted or made private. GitHub search for "hapag lloyd sdk" also returns 0 results.

**Note**: The PyPI package `hapag-lloyd-sdk` v0.2.1 still exists and is installable. Only the source repository link is broken.

**Impact**: The GitHub link in the Source Links table is dead. Community library table should note the repo is unavailable.

### 3. 🟡 Java Maven Artifacts Not Published on Maven Central
**Claim**: "Java: official Maven artifacts `com.hlag.api:openapi-specs` from GitHub"
**Reality**: Maven Central search (`search.maven.org`) returns **0 results** for `com.hlag.api`. The GitHub repo's `pom.xml` defines this groupId/artifactId but the artifact does not appear to be published.

**Impact**: Developers cannot actually use this as a Maven dependency. The claim is misleading.

### 4. 🟡 GitHub customer-api-definitions Description Misleading in Context
**Claim**: Presented as "Java: official Maven artifacts" alongside Track & Trace context
**Reality**: The repo's own description: *"Swagger / OpenAPI definitions for Customer APIs called by Hapag-Lloyd"* — note "called **by** Hapag-Lloyd". The subscription_callback.yaml explicitly states: *"This API is called by Hapag-Lloyd's systems and needs to be implemented by you to receive subscription notifications."*

This is a **callback API specification** — it defines the endpoint that a CUSTOMER must implement for Hapag-Lloyd to push rate sheet notifications TO them. It is NOT an SDK for calling Hapag-Lloyd's APIs.

**Impact**: The community libraries table conflates this with customer-facing API libraries. Should be clarified as "Rate Sheet notification callback spec (customer-implemented endpoint)".

### 5. 🟡 Mock Endpoint Returns Older Data Format Than v2.2.4 Spec
**Observation**: The mock server at `mock.api-portal.hlag.com` returns events in a format that differs from the v2.2.4 OpenAPI spec:
- Uses flat `eventTypeCode` (e.g., `"eventTypeCode":"GTOT"`) instead of separate `equipmentEventTypeCode`/`transportEventTypeCode`
- Uses `shipmentINformationTypeCode` (note the `IN` casing anomaly and different field name vs `shipmentEventTypeCode`)
- Returns simple `transportCallID: 6` instead of embedded `transportCall` objects

**Impact**: Developers building against the mock may produce code that doesn't work with the real v2.2.4 API. The "Sandbox/test environment" section should note this format discrepancy.

### 6. 🟢 Minor: Coverage Summary Partial-Counting Inconsistency
**Observation**: The coverage summary table counts ⚠️ (partial) items inconsistently:
- Section 1.x: "3/5 available" — excludes 1.3 and 1.4 (both ⚠️ partial)
- Section 3.x: "8/9 available" — appears to count 3.2–3.9 (all ✅) and exclude 3.1 (⚠️)

In both cases partials are excluded from the "available" count, which is consistent. However, the text note "(2 partial)" for 1.x and "(1 partial: inferred assignment)" for 3.x could be clearer about whether partials contribute to the overall 57% figure. Since 17/30 = 57% excludes all 3 partials, the math is internally consistent.

**Impact**: Minor clarity issue. No mathematical error.

---

## Corrections Needed

| # | Section | Current Text | Correction |
|---|---------|-------------|-----------|
| 1 | Carrier Overview → Alliance | "THE Alliance (with ONE, HMM, Yang Ming)" | **"Gemini Cooperation (with Maersk)"** — launched February 2025; HL left THE Alliance January 2025 |
| 2 | Community Libraries → Python SDK GitHub | `https://github.com/gedankenfabrik/hapag_lloyd_sdk` | Note: *"Source repo returns 404 (deleted/private as of Feb 2026). PyPI package still available."* |
| 3 | Community Libraries → Java | "Java: official Maven artifacts `com.hlag.api:openapi-specs` from GitHub" | Clarify: *"Java: Rate Sheet notification callback spec only (not a general API client library). OpenAPI definition for customer-implemented callback endpoint. Not published on Maven Central as of Feb 2026."* |
| 4 | Sandbox/test environment | "Yes — mock server at `https://mock.api-portal.hlag.com/v2/events`" | Add caveat: *"Mock server returns data in an older event format (v1.x style) that differs from the v2.2.4 OpenAPI spec. Developers should not rely on mock response structure for production code."* |
| 5 | Source Links → Python SDK GitHub | `https://github.com/gedankenfabrik/hapag_lloyd_sdk` | Mark as broken / 404 |

---

## Unable to Verify

- **Registration flow details** — Cannot test whether registration is truly instant/self-service without creating an account. The API description says it's open to all, but actual approval times, required information, etc. are unknown.
- **IP whitelisting** — Not documented; cannot confirm or deny without account access.
- **Aggregator claims** (project44, FourKites, Vizion, CargoSmart, INTTRA) — These are reasonable assumptions for a top-5 carrier but were not individually verified against each aggregator's carrier list.
- **EDI message type specifics** (IFTMIN, COPARN, BAPLIE, IFTSTA) — Standard for major carriers but not independently verified for Hapag-Lloyd specifically.
- **Historical data depth** — Not documented in public spec; requires authenticated testing.
- **Event freshness / latency** — Not documented; requires live testing.
- **Tier upgrade process** — Cannot verify how to move from Tryout to Default/Premium without contacting HL.
- **HL LIVE and Live Position commercial terms** — Cannot verify contract requirements without business engagement.
- **DCSA TNT Information Model v3.3 PDF content** — URL works (308→200 redirect) but the specific version mapping between HL's TNT v2.2.4 and DCSA IM v3.3 was not independently cross-checked.
- **Hapag-Lloyd website URLs** (tracking.html, offices page, API info page) — All return HTTP 403 from curl due to Cloudflare protection. Pages likely exist and work in browsers but cannot be verified programmatically. Consistent with the inventory's note about scraping difficulty.
- **SCAC code HLCU** — Common knowledge for the industry but not independently confirmed via an authoritative SCAC database.

---

## Summary

**Overall quality**: The inventory is **thorough and largely accurate**. The OpenAPI spec analysis, event code mappings, rate limit tiers, and BETA status documentation are all verified correct against the live API portal data.

**Critical correction needed**: The alliance membership (THE Alliance → Gemini Cooperation) is factually wrong as of the document date and should be corrected immediately.

**Moderate corrections**: The Python SDK GitHub link is dead (repo deleted/private), the Java Maven artifact claim is misleading (callback spec, not published on Maven Central), and the mock endpoint format discrepancy should be documented.

**Verification score**: Of the 11 checklist items reviewed:
- ✅ 8 fully verified accurate
- ⚠️ 2 partially accurate (community libraries have issues, mock endpoint exists but has caveats)
- ❌ 1 factually incorrect (alliance membership)
