# Grimaldi API Inventory — Review Findings

> **Reviewed**: 2026-02-11
> **Reviewer**: Audit review agent
> **Inventory reviewed**: `docs/carriers/grimaldi/api-inventory.md`
> **Severity**: 🔴 CRITICAL — The inventory's central claim is wrong. Grimaldi has a REST API.

---

## 🔴 CRITICAL Issue: Grimaldi HAS a REST API

The inventory's core thesis — that "Grimaldi Lines does not offer a public REST/SOAP/GraphQL API" — is **incorrect**. A REST API exists on the GNET platform.

**Evidence discovered during review:**

The Grimaldi Group operates a platform called **GNET** at `https://www.gnet.grimaldi-eservice.com` which hosts a documented WebAPI help page at:

> **https://www.gnet.grimaldi-eservice.com/services/Help**

This page documents the following REST API endpoints:

### Discovered API Endpoints

| Endpoint | Method | Description |
|----------|--------|-------------|
| `GET api/Security` | GET | **Authentication** — accepts UserID and UserSecret in request header, returns Bearer Security Token |
| `GET api/Security?Token={Token}` | GET | Token validation |
| `GET api/Info?ShipmentNo={ShipmentNo}` | GET | **Shipment info** — returns POL, POD, ETS, ETA |
| `GET api/Info?CntrNo={CntrNo}` | GET | **Container technical data** (requires Security Token) |
| `GET api/Info?ShipmentNo={ShipmentNo}&strCntrNo={strCntrNo}` | GET | **Container tracking** (Atlas) |
| `GET api/Info?ShipmentNo={ShipmentNo}&VehicleID={VehicleID}` | GET | **RoRo tracking** — from Grimaldi ERP (requires Security Token) |
| `GET api/Info?ShipmentNo={ShipmentNo}&VIN={VIN}` | GET | **RoRo tracking events** — e.g. OnQuay, Loaded (requires Security Token) |
| `GET api/Info?VoyageNo={VoyageNo}` | GET | **Sailing schedule by voyage** (requires Security Token) |
| `GET api/Info?VesselCode=...&DtETSFrom=...&DtETSTo=...&PortCode=...` | GET | **Sailing schedule by vessel/date/port** |
| `GET api/Info?POL={POL}&POD={POD}&nDays={nDays}&...` | GET | **Point-to-point schedule** |
| `GET api/Vessel?UnRestricted={UnRestricted}` | GET | Vessel information |
| `GET api/DTS?VIN={VIN}` | GET | Vehicle tracking by VIN |
| `GET api/DTS?ShipmentNumber={ShipmentNumber}` | GET | Vehicle tracking by shipment number |
| `GET api/DTS?ChassisNo={ChassisNo}` | GET | Vehicle tracking by chassis number |
| `GET api/Document?DocumentID={DocumentID}` | GET | Document retrieval |
| `GET api/AtlasVerification?ShipmentNo=...&WAFreleasePIN=...` | GET | WAF release PIN verification |

### Authentication
- **Mechanism**: Bearer token
- **Flow**: `GET api/Security` with UserID + UserSecret in request header → returns Security Token → use as Bearer token in subsequent requests
- **Registration**: Likely requires business relationship (not self-service), but the API itself is a standard REST API with token auth

### GNET Platform Also Provides (Web Portal)
- Container Tracking (public, no login required for basic tracking)
- RoRo Tracking
- Shipment ETA/ETS
- Container Technical Data
- VGM submission
- Voyage Intake (VSM)
- Point-to-Point Schedule Enquiry
- Published sailing schedules (all major routes — W. Africa, South America, Mediterranean, N. America, Far East, etc.)

**Impact**: This invalidates the entire inventory. The "No API" conclusion, the "0% coverage" assessment, the "aggregator-only" recommendation, and the SDK adapter infeasibility determination are all wrong. A direct Grimaldi adapter IS feasible.

---

## Issues Found

### Issue 1: 🔴 Missed GNET Platform and API Entirely
- **Location**: Entire document — every section builds on the false premise of no API
- **Problem**: The GNET platform at `gnet.grimaldi-eservice.com` was not discovered during research. This platform hosts both a web portal and a REST API with documented endpoints.
- **Root cause**: Research focused on `grimaldi-lines.com` (primarily a passenger ferry site) and general web searches. The GNET platform lives on a different domain (`grimaldi-eservice.com`) linked from the corporate site `grimaldi.napoli.it`.

### Issue 2: 🔴 Wrong Tracking URL
- **Location**: Web Portal Tracking section
- **Inventory says**: Tracking URL is `grimaldi-lines.com` with "exact path could not be verified without login"
- **Actually**: The primary container tracking portal is at `https://www.gnet.grimaldi-eservice.com/gnet/pages_gatlas/wfcontainertracking` (publicly accessible, no login required for basic tracking). Lookup supports both Equipment # (container) and Shipment #.

### Issue 3: 🟡 Missed cargo.grimaldi-lines.com
- **Location**: Web Portal Tracking section
- **Problem**: A separate cargo-focused subdomain exists at `https://cargo.grimaldi-lines.com` for short-sea RoRo/ferry cargo services (schedules, driver cards, etc.). This is distinct from the deep-sea container tracking at GNET.

### Issue 4: 🟡 Missed booking.grimaldi-lines.com
- **Location**: Not mentioned at all
- **Problem**: An online booking portal exists at `https://booking.grimaldi-lines.com/index.php?l=en`. This was not documented.

### Issue 5: 🟡 DCSA Founding Member Count Error
- **Location**: DCSA member table — "The 10 founding/current members are: MSC, Maersk, CMA CGM, Hapag-Lloyd, ONE, Evergreen, Yang Ming, HMM, ZIM, and PIL"
- **Problem**: DCSA had **9 founding members** in April 2019. PIL joined later (2024). Calling all 10 "founding" members is inaccurate.
- **DCSA source**: dcsa.org/about-us states: "In April of 2019, MSC, Maersk, CMA CGM, Hapag-Lloyd, ONE, Evergreen, Yang Ming, HMM and ZIM founded DCSA"
- **Correction**: "The 9 founding members are MSC, Maersk, CMA CGM, Hapag-Lloyd, ONE, Evergreen, Yang Ming, HMM, and ZIM. PIL became the 10th member in 2024."

### Issue 6: 🟡 DCSA Link Uses Wrong Path
- **Location**: Source Links table — lists `https://dcsa.org/about/`
- **Problem**: The actual page is at `https://dcsa.org/about-us/`. The `/about/` path does redirect correctly, but the canonical URL is different.
- **Correction**: Change to `https://dcsa.org/about-us/`

### Issue 7: 🟢 grimaldi-lines.com Redirects to Italian
- **Location**: Carrier Overview — Website URL
- **Problem**: `grimaldi-lines.com` redirects to `/it/` (Italian). The English version is at `grimaldi-lines.com/en/`. Minor issue but worth noting since the site is primarily a passenger ferry booking site, not the cargo/container operations portal.

### Issue 8: 🟡 Coverage Matrix Needs Complete Rework
- **Location**: Required Fields Coverage Matrix (all sections)
- **Problem**: The entire coverage matrix is based on "no API" and rates everything as ❌ or ❓ for API access. With the GNET API, many fields are actually available programmatically:
  - Shipment info (POL, POD, ETA, ETS) → fields 4.1, 4.2 likely ✅
  - Container tracking milestones → fields 3.x likely partially available
  - Sailing schedules (voyage + point-to-point) → field 1.1 likely ✅
  - Point-to-point routes → field 1.2 likely ✅

### Issue 9: 🟢 Grimaldi.napoli.it Container Department Page Not Mentioned
- **Location**: Not documented
- **Finding**: `https://www.grimaldi.napoli.it/container/` hosts a Container Department page with links to container tracking, equipment location, demurrage management, and container specifications.

---

## Verified Accurate

- ✅ **DCSA non-membership** — Grimaldi is confirmed NOT a DCSA member. Verified against dcsa.org/about-us/members which lists only: MSC, Maersk, CMA CGM, Hapag-Lloyd, ONE, Evergreen, Yang Ming, HMM, ZIM, PIL.
- ✅ **ACL relationship** — ACL (aclcargo.com) is confirmed as a Grimaldi Group subsidiary with its own web presence. The claim that ACL operates independently with SCAC ACLU is consistent with industry knowledge.
- ✅ **RoRo specialist claim** — Confirmed. The GNET platform has both container tracking AND RoRo tracking (by VIN, vehicle ID, chassis number), confirming Grimaldi handles both. The cargo.grimaldi-lines.com portal is entirely focused on RoRo/truck transport.
- ✅ **Alliance independence** — Grimaldi is not a member of any shipping alliance. Confirmed.
- ✅ **All listed URLs load** — grimaldi-lines.com (HTTP 200), aclcargo.com (HTTP 200), grimaldi.napoli.it (HTTP 200), vizionapi.com (HTTP 200), terminal49.com (HTTP 200).
- ✅ **No template placeholders found** — No `[TODO]`, `[TBD]`, `{CARRIER}`, or other unfilled template markers.
- ✅ **SCAC code GRIM** — Consistent with steamship-lines.md and industry databases.
- ✅ **Naples, Italy headquarters** — Confirmed on corporate site and cargo portal (via Marchese Campodisola 13, Napoli).
- ✅ **Family-owned** — Confirmed. Grimaldi Group SpA corporate structure shown on multiple portals.

---

## Corrections Needed

### Priority 1: Reclassify carrier — API EXISTS
The inventory must be substantially reworked. Key changes:
1. **Digital Ecosystem section**: Document the GNET developer portal at `gnet.grimaldi-eservice.com/services/Help`
2. **API endpoint reference**: Document all discovered REST endpoints
3. **Authentication**: Document Bearer token auth flow (UserID + UserSecret → Security Token)
4. **Coverage matrix**: Re-evaluate all 30 fields against the API capabilities
5. **SDK adapter recommendations**: Reclassify from "infeasible" to "feasible — direct adapter possible"
6. **Web portal tracking section**: Update with correct URL (`gnet.grimaldi-eservice.com`)

### Priority 2: Factual corrections
1. DCSA founding member count: 9 founders, not 10 (PIL joined 2024)
2. DCSA link: Change `dcsa.org/about/` to `dcsa.org/about-us/`
3. Tracking URL: Change from `grimaldi-lines.com` to `gnet.grimaldi-eservice.com/gnet/pages_gatlas/wfcontainertracking`
4. Add cargo.grimaldi-lines.com and booking.grimaldi-lines.com to source links
5. Add grimaldi.napoli.it/container/ to source links

### Priority 3: Impact on steamship-lines.md
The `docs/steamship-lines.md` entry for Grimaldi currently shows:
- **API Status**: None → Should be updated to reflect discovered API
- **Auth Type**: N/A → Should be "Bearer Token"
- **Developer Portal**: "None — no developer portal" → Should reference GNET API

---

## Unable to Verify

- **API access requirements** — Whether the GNET API is available to any registered user or requires a business partnership is unknown. The Security endpoint documentation suggests token-based auth, but registration details are not publicly documented.
- **API response format/payloads** — The help page lists endpoints but most say "No documentation available." The actual response structures, field names, and data quality cannot be verified without credentials.
- **API rate limits** — Not documented.
- **Aggregator availability** — Whether Vizion, Terminal49, or other aggregators include Grimaldi remains unverified. However, given that a direct API exists, this is less critical.
- **EDI support details** — No public EDI documentation found. The claim of "likely yes" is reasonable but unverified.
- **GNET API completeness** — The help page shows discovered endpoints, but there may be additional endpoints behind authentication. The "EVOYAGE" section in the menu suggests additional voyage-related API functionality.
- **INTTRA membership** — Could not confirm or deny.
- **Whether the GNET API is "public" or "partner-only"** — The API help page is publicly accessible, but actual API calls require a Security Token obtained with UserID/UserSecret. Whether these credentials are available to any business partner or only select customers is unclear.
