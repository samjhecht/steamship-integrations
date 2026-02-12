# Evergreen API Inventory — Review Findings

> **Reviewed**: 2026-02-11
> **Reviewer**: Audit review agent
> **Inventory reviewed**: `docs/carriers/evergreen/api-inventory.md`
> **Severity**: 🟢 GOOD — Inventory is substantially accurate. Minor corrections only.

---

## Verified Accurate

### Core Claims — All Confirmed

- ✅ **DCSA founding member** — VERIFIED. dcsa.org/about-us confirms: "In April of 2019, MSC, Maersk, CMA CGM, Hapag-Lloyd, ONE, Evergreen, Yang Ming, HMM and ZIM founded DCSA." The inventory correctly states "one of 9 carriers that established DCSA in April 2019." This is accurate — 9 founding members, not 10 (PIL joined later).

- ✅ **ShipmentLink API Portal exists** — VERIFIED. `https://www.shipmentlink.com/_ec/APIPORTAL_Home` returns HTTP 200. The portal is publicly accessible and shows API documentation links.

- ✅ **Full DCSA suite (4 APIs)** — VERIFIED. The API Service Category page at `shipmentlink.com/_ec/APIPORTAL_APIServiceCategory` lists all four:
  1. **Track & Trace** — with Swagger link to `APIPORTAL_SpecTrackAndTrace`
  2. **Booking** — with Swagger link to `APIPORTAL_SpecBK`
  3. **Shipping Instructions** — with Swagger link to `APIPORTAL_SpecSI`
  4. **Commercial Schedules** — with Swagger link to `APIPORTAL_SpecCommercialSchedules`

- ✅ **JWT authentication** — VERIFIED. FAQ item 3 confirms: "you may receive a confirmation letter with Client ID & secret get token (JWT) for the pull/push API service… Evergreen will return you a token with JWT format (https://jwt.io)."

- ✅ **IP whitelisting required** — VERIFIED. FAQ item 4 states: "Please make sure your IP address is the same as the one filled in your requirement form and retry again."

- ✅ **Track & Trace is free** — VERIFIED. FAQ item 9: "API Track & Trace is a free service at present."

- ✅ **Historical data limitation** — VERIFIED. FAQ item 10: "Only events that happen after launch time can be traced." FAQ item 11 confirms no past event retrieval but notes new events appear for pre-existing active shipments.

- ✅ **Application/approval registration process** — VERIFIED. FAQ item 1 describes the multi-step process: application form → Evergreen staff verification → service confirmation with credentials.

- ✅ **DCSA affiliation stated by Evergreen** — VERIFIED. FAQ item 8: "Digital Container Shipping Association (DCSA) is a nonprofit, independent organisation established in 2019 by several of the largest container shipping companies, including Evergreen."

### Coverage Summary Math — Verified

| Category | Claimed | Verified |
|----------|---------|----------|
| API Domains (1.x) | 4/5 available, 1 unavailable → 80% | ✅ Correct (only Rates missing) |
| Cutoffs (2.x) | 6/6 available → 100% | ✅ Correct |
| Container Milestones (3.x) | 6/9 full, 3/9 partial → 67% | ✅ Correct (3.1 partial, 3.5 partial, 3.6 partial) |
| Transport Events (4.x) | 2/4 full, 2/4 partial → 50% | ✅ Correct (4.3 partial, 4.4 partial) |
| Change Detection (5.x) | 4/6 supported, 1 unknown, 1 partial → 67% | ✅ Correct |
| **Total** | **22/30 = 73%, 97% with inference** | ✅ Math checks out (29/30 = 96.7%) |

### URLs — All Load Successfully

| URL | HTTP Status | Notes |
|-----|-------------|-------|
| evergreen-line.com | 200 | ✅ |
| evergreen-marine.com | 200 | ✅ |
| shipmentlink.com | 200 | ✅ |
| shipmentlink.com/_ec/APIPORTAL_Home | 200 | ✅ |
| shipmentlink.com/_ec/APIPORTAL_APIServiceCategory | 200 | ✅ |
| shipmentlink.com/_ec/APIPORTAL_FAQ | 200 | ✅ |
| shipmentlink.com/_ec/APIPORTAL_SpecTrackAndTrace | 200 | ✅ |
| shipmentlink.com/_ec/APIPORTAL_SpecBK | 200 | ✅ |
| shipmentlink.com/_ec/APIPORTAL_SpecSI | 200 | ✅ |
| shipmentlink.com/_ec/APIPORTAL_SpecCommercialSchedules | 200 | ✅ |
| ct.shipmentlink.com/servlet/TDB1_CargoTracking.do | 200 | ✅ |
| shipmentlink.com/tam1/jsp/TAM1_EdiRegisterNotice.jsp | 200 | ✅ |
| shipmentlink.com/_apiportal/spec/doc/EMC_TNT_2.2_Event_List.xlsx | 200 | ✅ |
| dcsa.org/about-us/ | 200 | ✅ |
| dcsa.org/wp-content/uploads/.../DCSA-Subscription-Callback-API_1.0_Final.pdf | 200 | ✅ (redirects to CDN) |
| e2open.com | 200 | ✅ |

### No Template Placeholders
- ✅ No `[TODO]`, `[TBD]`, `{CARRIER}`, or unfilled template markers found. The one grep match for "fill in" is legitimate FAQ text ("you need to fill in the API service application form").

---

## Issues Found

### Issue 1: 🟡 DCSA Subscription PDF URL Redirects to CDN
- **Location**: Source Links table — DCSA Subscription/Callback Spec PDF
- **Listed URL**: `https://dcsa.org/wp-content/uploads/2021/10/202109_DCSA-Subscription-Callback-API_1.0_Final.pdf`
- **Actual final URL**: `https://dcsa-website.cdn.prismic.io/dcsa-website/65b0a969615e73009ec3df05_202109_DCSA-Subscription-Callback-API_1.0_Final.pdf`
- **Impact**: Low — the listed URL still works (returns HTTP 200 via redirect). But the direct link may break if DCSA changes their redirect rules. Consider noting it's a redirect.

### Issue 2: 🟡 TNT v2.2 Version — Minor Documentation Gap
- **Location**: DCSA section — claims TNT v2.2
- **Finding**: The inventory correctly identifies this as DCSA TNT v2.2 and notes it's older than v3. However, the Swagger page itself could not be deeply inspected to verify the exact `"swagger": "2.0"` or `"version": "2.2"` headers from a pure curl check (the Swagger UI renders dynamically). This claim is consistent with:
  - The event list spreadsheet filename: `EMC_TNT_2.2_Event_List.xlsx`
  - The endpoint path prefix `/tnt/v2/events`
  - The "Last Updated 2023-11-15" note
  - The `API-Version: "2.2"` header requirement mentioned
- **Verdict**: Claim is very likely accurate based on multiple corroborating evidence points.

### Issue 3: 🟢 Retracted Events Classification Inconsistency
- **Location**: Change Detection section (5.x) and Coverage Summary
- **Finding**: "Retracted Events" is marked ❓ in the Change Detection table but the coverage summary counts it in the "Fields Partial/Unknown" column (7 total). This is internally consistent — ❓ fields count as "partial/unknown" — but the summary note says "4/6 supported" when it should more precisely say "4 supported, 1 unknown (retracted), 1 unknown (retry on failure for webhooks is a different thing)." The 4/6 = 67% math is correct.

### Issue 4: 🟢 "PLAN" vs "PLN" — Event Example Ambiguity
- **Location**: Known Issues section
- **Finding**: The inventory notes that "Event examples from Evergreen use `"PLAN"` in some cases where DCSA standard specifies `"PLN"`." This is flagged appropriately. However, this could also be a documentation error in the example payloads rather than actual API behavior. Without live API access, it's impossible to confirm whether the API actually returns `"PLAN"` or `"PLN"`. The inventory correctly recommends handling both in the SDK adapter.

---

## Corrections Needed

### Priority 1 (Minor)
None — no factual errors requiring immediate correction were found.

### Priority 2 (Suggested Improvements)

1. **Source Links**: Consider adding a note that the DCSA Subscription PDF URL redirects to a CDN. The current URL works but may be fragile.

2. **Coverage Summary footnote**: The "97% with inference" claim is well-documented in the note below the table, but could benefit from explicitly listing which field is the 1 truly unavailable one (Rates, 1.5) in the summary table itself for quick scanning.

3. **Aggregator section**: The inventory correctly notes "Unknown" for all aggregators. Given Evergreen is the 7th largest carrier globally and an Ocean Alliance member, it's almost certain that major aggregators (project44, Vizion, Terminal49) support Evergreen. The "Unknown" rating is technically correct but could note the high probability.

4. **Testing/sandbox**: FAQ item 2 mentions "testing progress" and "confirmation of the testing" before go-live, suggesting there IS some form of testing phase. The inventory says "Unknown" for sandbox which is technically correct, but could reference FAQ item 2 as a hint that testing infrastructure exists during onboarding.

---

## Unable to Verify

- **OpenAPI spec version headers** — Could not extract the exact Swagger/OpenAPI version strings from the spec pages (they render via JavaScript). The claim of "Swagger 2.0 for TNT" and "OpenAPI 3.0.3 for others" is consistent with all observed evidence but not directly confirmed from the spec file headers.
- **Rate limits** — Inventory correctly states "Unpublished / Unknown." Cannot verify or deny.
- **Token lifetime** — Not documented. Cannot verify.
- **Webhook retry policy** — Not documented. Cannot verify.
- **Sandbox environment** — Cannot confirm existence without API credentials.
- **Booking/SI/CS pricing** — Only T&T is confirmed free. Other API pricing unknown.
- **Actual live API behavior** — All findings are based on documentation review. Without credentials, we cannot verify response formats, event classifier codes (PLAN vs PLN), or data quality.
- **Specific DCSA version compliance** — While the API categories match DCSA standards (TNT, BKG, SI, CS), the degree of conformance to specific DCSA spec versions cannot be verified without live testing.

---

## Overall Assessment

The Evergreen API inventory is **well-researched and substantially accurate**. The core claims — DCSA founding membership, full API suite availability, JWT auth, IP whitelisting, free T&T, coverage percentages — are all verified. The document is thorough, well-structured, includes appropriate caveats where information couldn't be confirmed, and provides actionable SDK adapter recommendations.

**Quality rating: High** — This inventory can be used as-is for planning SDK adapter implementation, with the understanding that live API testing during onboarding will validate the documentation-based claims.
