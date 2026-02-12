# MSC API Inventory — Review Findings

**Reviewer**: Automated audit agent
**Date**: 2026-02-11
**Document reviewed**: `docs/carriers/msc/api-inventory.md`
**Methodology**: Web verification of claims via curl against live URLs, Wayback Machine archives, developer portal HTML scraping, and cross-referencing DCSA sources.

---

## Verified Accurate

### Developer Portal & URLs
- **developerportal.msc.com exists and loads** (HTTP 200). Title confirms "Developer Portal - MSC". Navigation includes Home, API Catalogue, Subscription, EDI Guidelines, FAQ — matching inventory claims.
- **All developer portal sub-pages return HTTP 200**: `/api-catalogue`, `/subscription`, `/edi-guideline`, `/faq`, `/content/Data_Protection_Notice_Direct_Integration.pdf`.
- **Direct Integration Request Form** (forms.office.com) returns HTTP 200 — confirmed accessible.
- **mymsc.com** returns HTTP 200.
- **github.com/dcsaorg** returns HTTP 200.
- **dcsa.org/about-us** returns HTTP 200.
- **inttra.com** returns HTTP 200.
- **cargosmart.com** returns HTTP 200.
- **www.msc.com returns 403** for automated requests — confirms Akamai bot protection claim.

### DCSA Compliance
- **DCSA founding member**: Confirmed. Wayback Machine capture of MSC Direct Integrations page states: "As a founding member of the DCSA, MSC plays an important role in creating and adopting DCSA standards."
- **Founded April 2019**: Confirmed in same text.
- **DCSA TNT v2.2**: Confirmed. Wayback Machine shows card "Track and Trace" with "DCSA T&T v2.2". Developer portal API catalogue lists `DPO-DCSATrackAndTrace-API-V2`.
- **DCSA Commercial Schedules**: Confirmed. Wayback Machine shows "Commercial Vessel Schedules" with "DCSA Commercial Schedules 1.0". Developer portal lists `DPO-Commercial-Schedules-API-V1`.
- **DCSA Booking 2.0 — in development**: Confirmed. Wayback Machine (Oct 2025) shows both "Booking Request" and "Booking Confirmation" cards with "DCSA Booking 2.0 - In development - planned for release by end of 2025".
- **DCSA BL 3.0 — in development**: Confirmed. Wayback Machine shows "Shipping Instructions" and "Bill of Lading Data" cards with "DCSA BL 3.0 - In development - planned for release by end of 2025".

### Azure Service Bus
- **Confirmed**. The developer portal `/subscription` page explicitly states: "Understand how to onboard on to MSC service bus subscription" and links a PDF titled "TnT_Subscription_Customer_Onboarding.pdf" described as: "The document provides the information necessary to onboard on the Track and Trace Azure Service Bus Subscription" (Version 1.0, updated March 27th 2025). This is a key claim that checks out.

### API Access Model
- **Formal onboarding required for production**: Confirmed. Developer portal prominently links the Direct Integration Request Form. Wayback Machine FAQ states "Once received, MSC team will contact you to start the onboarding process (fees may apply)."
- **Fees may apply**: Confirmed. Wayback Machine shows both "*Fees may apply" footnote and FAQ answer: "Depending on your requirements, setup and maintenance fees may apply."
- **24/7 support**: Confirmed. FAQ states: "MSC provides 24/7 support for the direct integrations we offer to our partners and customers."
- **Sandbox/test environment**: Confirmed. Developer portal homepage text describes: "Try out easy to navigate sandbox (test environment) that simulates responses and calls to applications in real-time." FAQ confirms: "In the sandbox you can see the exact data structure and data elements as the API offers in production, however in a predefined set of production-like mock data."

### Third-Party Aggregators
- **INTTRA, InforNexus, CargoSmart — confirmed as connection partners**. Wayback Machine capture explicitly lists all three: "This includes developing direct integration solutions for INTTRA, InforNexus and CargoSmart" and links to each under "Connection partners". Exactly as documented.

### EDI Message Types
All 12 EDI message types documented in the inventory are **confirmed on the developer portal EDI Guidelines page**:
- EDIFACT: IFTMBF, IFTMBC, IFTMIN, VERMAS, IFTMCS, IFTSAI, IFTSTA, APERAK — all 8 confirmed.
- X12: 300, 301, 315, 323 — all 4 confirmed.
- The developer portal's `data-filter-name` attributes and downloadable PDFs confirm each message type.

### Other Verified Claims
- **SMDG and BIC standards**: Confirmed. FAQ states "MSC follows different standards such as SMDG (for terminal codes), BIC (for depots), and others (including internal codes)."
- **RSS Feed migration**: Confirmed. FAQ mentions "current RSS Feed (web services) customer" and migration assistance.
- **iReefer Ultimate**: Confirmed on subscription page with update date "August 14th 2025".
- **SFTP and AS2 for EDI**: Confirmed. Wayback Machine states "EDIs enable large amounts of information to be exchanged as data files via secure protocols, such as SFTP and AS2."
- **SCAC Code MSCU**: Standard industry fact, no contradictory evidence found.
- **Geneva headquarters**: Standard industry fact, confirmed.

### Coverage Matrix Field Mappings (Spot-Check)
- **Cutoff codes** (ECP, EFC, DCO, VCO, FCO, LCO): These are standard DCSA cutoff codes per the DCSA Commercial Schedules spec. Mapping is correct.
- **Equipment event codes** (GTOT, GTIN, LOAD, DISC): Standard DCSA TNT v2.2 codes. Mapping is correct.
- **Transport event codes** (DEPA, ARRI) with classifiers (PLN, EST, ACT): Standard DCSA TNT v2.2 pattern. Mapping is correct.
- **Event subscription endpoint** (`POST /v2/event-subscriptions`): Consistent with DCSA TNT v2.2 spec.
- **Container lookup via `equipmentReference`**: Standard DCSA TNT v2.2 parameter name. Correct.

---

## Issues Found

### ISSUE 1 (HIGH): MSC Market Position — Contradictory Statement
**Location**: Carrier Overview table
**Claim**: "Second-largest container carrier globally by TEU capacity; surpassed Maersk in fleet size in 2022"
**Problem**: This is internally contradictory. If MSC surpassed Maersk in 2022, it would be the **largest**, not "second-largest." MSC overtook Maersk in January 2022 and has been the world's largest container shipping line by TEU capacity since then.

### ISSUE 2 (HIGH): 2M Alliance — Outdated
**Location**: Carrier Overview table, "Alliance" row
**Claim**: "2M (with Maersk)"
**Problem**: The 2M Alliance between MSC and Maersk officially ended on **January 31, 2025**. As of the document date (Feb 2026), MSC is not a member of any shipping alliance. Maersk announced in January 2023 it would not renew, and the phase-out completed January 2025. MSC now operates independently with the Gemini Cooperation being Maersk's new arrangement (with Hapag-Lloyd), not including MSC.

### ISSUE 3 (HIGH): Rates API Incorrectly Marked as Unavailable
**Location**: Section 1.5 (API Domains) — marked "❌"
**Claim**: "No public rate API found."
**Problem**: This is incorrect. Two independent sources confirm rate API availability:
1. **Developer Portal API Catalogue** lists `dpo-rateintegration-api-v1` (Rate Integration API).
2. **Wayback Machine (Oct 2025)** shows a card titled "Contract Rates & Spot Rates" with description "MSC Contract Rates (pilot)".
**Impact**: Field 1.5 should be changed from ❌ to ⚠️ (at minimum — pilot/partial). This also affects the coverage summary math.

### ISSUE 4 (HIGH): Four APIs Missing from Inventory
**Location**: Entire document — Endpoint Reference section
**Problem**: The developer portal API Catalogue lists **6 APIs**, but only 2 are documented (TNT and Commercial Schedules). Four are completely missing:

| API ID | Name | Status |
|--------|------|--------|
| `DPO-MSCReporting-API-V1` | MSC Reporting API v1 | Not mentioned anywhere in inventory |
| `dpo-rateintegration-api-v1` | Rate Integration API v1 | Contradicts "Rates ❌" claim |
| `dpo-referential-api-v1` | Referential API v1 | Not mentioned anywhere in inventory |
| `dpo-mscroutingsailings-api-v1` | Routing/Sailings API v1 | Not documented as a separate API (may overlap with Commercial Schedules but is a distinct endpoint) |

These were all discoverable from the developer portal homepage HTML.

### ISSUE 5 (MEDIUM): Coverage Summary Math Affected
**Location**: Coverage Summary table
**Claim**: "Total: 27/30 = 90%"
**Problem**: If Rates (1.5) is changed from ❌ to ⚠️, the "Fields Unavailable" count drops from 1 to 0, and "Fields Available" or "Partial" increases. Additionally, the Routing/Sailings API may be relevant to 1.1 and 1.2 beyond just Commercial Schedules. The 90% figure needs recalculation after corrections.

### ISSUE 6 (LOW): SwaggerHub TNT v2.2 URL May Be Broken
**Location**: Source Links table, "DCSA TNT v2.2 OpenAPI Spec"
**URL**: `https://app.swaggerhub.com/apis/dcsaorg/DCSA_TNT/2.2.0`
**Problem**: Returns HTTP 302 redirect and then times out when following. The URL may have changed or SwaggerHub may have restructured their API hosting. The DCSA organization has been moving specs to GitHub; check `https://github.com/dcsaorg/DCSA-OpenAPI/tree/master/tnt` for the current location.

### ISSUE 7 (LOW): DCSA Schedules Version Can Be Made More Precise
**Location**: Standards Compliance → DCSA section
**Claim**: "Specific version not stated; likely v1.x based on DCSA standard timeline."
**Correction**: The Wayback Machine capture explicitly shows "DCSA Commercial Schedules 1.0" — the version is confirmed as **1.0**, not "likely v1.x".

---

## Corrections Needed

### Correction 1: Fix Market Position
**Current**: "Second-largest container carrier globally by TEU capacity; surpassed Maersk in fleet size in 2022"
**Correct to**: "Largest container carrier globally by TEU capacity (surpassed Maersk in January 2022)"

### Correction 2: Update Alliance Status
**Current**: "2M (with Maersk)"
**Correct to**: "None (formerly 2M with Maersk, ended January 2025)"

### Correction 3: Update Rates Field (1.5)
**Current**: "❌ | N/A | N/A | Not part of DCSA | MSC offers 'Instant Quote' via myMSC platform (web only). No public rate API found."
**Correct to**: "⚠️ | Rate Integration API v1 | `dpo-rateintegration-api-v1` | Not part of DCSA | MSC has a Rate Integration API v1 on the developer portal and listed 'Contract Rates & Spot Rates — MSC Contract Rates (pilot)' on the Direct Integrations page (Oct 2025). Details require onboarding. Instant Quotes also available via myMSC web portal."

### Correction 4: Add Missing APIs to Endpoint Reference
Add documentation for these four APIs visible in the developer portal catalogue:
1. **MSC Reporting API v1** (`DPO-MSCReporting-API-V1`) — purpose unknown from public HTML, needs investigation during onboarding.
2. **Rate Integration API v1** (`dpo-rateintegration-api-v1`) — Contract Rates & Spot Rates per Direct Integrations page.
3. **Referential API v1** (`dpo-referential-api-v1`) — purpose unknown from public HTML (likely reference/master data), needs investigation during onboarding.
4. **Routing/Sailings API v1** (`dpo-mscroutingsailings-api-v1`) — appears to be a separate API from Commercial Schedules. May be MSC-specific (non-DCSA) routing endpoint. Needs investigation during onboarding.

### Correction 5: Recalculate Coverage Summary
After Correction 3, recalculate:
- API Domains: 2 available + 1 partial (Rates) + 2 unknown = still ambiguous, but "unavailable" drops from 1 to 0
- Total available/partial: 28/30 (or 27+1 partial)
- Update coverage percentage accordingly

### Correction 6: Fix DCSA Schedules Version
**Current**: "Specific version not stated; likely v1.x"
**Correct to**: "v1.0 (confirmed on MSC Direct Integrations page as 'DCSA Commercial Schedules 1.0')"

### Correction 7: Note SwaggerHub URL Status
Add a note that the SwaggerHub URL (`app.swaggerhub.com/apis/dcsaorg/DCSA_TNT/2.2.0`) may be redirecting or broken. Check DCSA GitHub for current spec location.

---

## Unable to Verify

### Claims That Could Not Be Confirmed or Denied

1. **DCSA Booking 2.0 current status**: The inventory correctly notes this was "planned for end of 2025" — whether it's now live (Feb 2026) cannot be determined without onboarding or finding updated public sources.

2. **Authentication mechanism**: The inventory says "likely OAuth2 or API key with Azure-based identity management." This is reasonable speculation but unverifiable without onboarding.

3. **Rate limits**: Correctly documented as "Unpublished" — no public rate limit information was found in any source.

4. **DCSA founding member list accuracy**: The inventory lists "Maersk, CMA CGM, Hapag-Lloyd, ONE, Evergreen, Yang Ming, HMM, and ZIM" alongside MSC. DCSA's about-us page loads (HTTP 200) but member details are rendered via JavaScript (Prismic CMS) and not extractable via curl. The commonly cited list of 9 founding members matches but ZIM's inclusion as a day-one founder (vs. early joiner) could not be precisely verified.

5. **Sandbox public accessibility**: The developer portal describes the sandbox and "Try it Out" functionality. Whether it requires login/registration to actually use could not be determined from static HTML alone.

6. **project44, FourKites, Vizion coverage**: Correctly marked as "likely" — MSC's pages don't mention these aggregators. The inventory's qualifying language ("near-certain given market position") is reasonable.

7. **EDI 315 N9*SCA change (September 2024)**: Not verifiable from public sources without access to the actual MIG PDF. The inventory attributes this to MSC's published guidelines.

8. **Token lifetime, refresh mechanism, IP whitelisting**: All correctly marked as "Unknown" — only determinable during onboarding.

9. **Historical data depth**: Correctly marked as "Unknown."

10. **TradeLens shutdown timeline**: Inventory claims TradeLens was shut down in 2023 with MSC's page returning 404/301. This is consistent with known industry events (TradeLens announced discontinuation November 2022, ceased operations Q1 2023), but specific MSC URL behavior was not re-verified.

11. **APERAK (Functional Acknowledge)**: Confirmed present in EDI Guidelines on the developer portal (`data-filter-name="functional acknowledge aperak d99b edifact"`), but the claim that MSC actively uses it for data exchange could not be verified beyond its listing.

---

## Summary

| Category | Count |
|----------|-------|
| Claims verified accurate | 25+ |
| Issues found | 7 |
| High-severity issues | 4 |
| Medium-severity issues | 1 |
| Low-severity issues | 2 |
| Corrections needed | 7 |
| Unable to verify | 11 |

**Overall Assessment**: The inventory is **substantially accurate** in its core claims about DCSA compliance, developer portal structure, Azure Service Bus, third-party aggregators, and EDI support. However, there are **four high-severity issues** that need correction: the contradictory market position, the outdated alliance status, the incorrectly marked Rates API, and four undocumented APIs that are visible on the developer portal. The most impactful finding is that MSC has significantly more API surface area than documented — the developer portal lists 6 APIs versus the 2 covered in the inventory.
