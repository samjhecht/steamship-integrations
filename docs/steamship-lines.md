# Steamship Lines Index

Quick-reference table for all carriers SSL works with. Detailed per-carrier information lives in `docs/carriers/<carrier>/api-inventory.md`.

> **Column definitions:**
> - **SCAC**: Standard Carrier Alpha Code — the industry identifier our SDK uses
> - **Parent / Alliance**: Corporate ownership and alliance membership (2M, THE Alliance, Ocean Alliance)
> - **API Status**: None / Tracking Only / Full Suite / Portal Only
> - **DCSA**: DCSA compliance — Yes (version) / Partial / No / Unknown
> - **Auth Type**: OAuth2 / API Key / Bearer / Basic / Partner Required / N/A
> - **Audit**: Not Started / In Progress / Complete
> - **Adapter**: Not Started / In Progress / Complete

## Priority Carriers

| Carrier Name | SCAC | Parent / Alliance | API Status | DCSA | Auth Type | Developer Portal | Audit | Adapter |
|---|---|---|---|---|---|---|---|---|
| Maersk Line | MAEU | Maersk / 2M (now Gemini w/ Hapag-Lloyd) | Full Suite | Yes (TNT v3, BKG v2, CS v1) | OAuth2 + API Key | [developer.maersk.com](https://developer.maersk.com) | Complete | Not Started |
| Mediterranean Shipping Company (MSC) | MSCU | MSC Group / 2M | Tracking + Schedules | Yes (TNT v2.2, CS) | Partner Required (onboarding) | [developerportal.msc.com](https://developerportal.msc.com/) | Complete | Not Started |
| Hapag-Lloyd | HLCU | Hapag-Lloyd / THE Alliance | Full Suite (BETA) | Yes — TNT v2.2, CS v1.0, OVS v3.0 | API Key (IBM API Connect) | [api-portal.hlag.com](https://api-portal.hlag.com) | Complete | Not Started |
| Grimaldi Lines | GRIM | Grimaldi Group / Independent | None | No | N/A | None — no developer portal | Complete | Not Started |
| Evergreen Line | EGLV | Evergreen Group / Ocean Alliance | | | | | Not Started | Not Started |

## All Carriers

| Carrier Name | SCAC | Parent / Alliance | API Status | DCSA | Auth Type | Developer Portal | Audit | Adapter |
|---|---|---|---|---|---|---|---|---|
| Aliança | | Maersk | | | | | Not Started | Not Started |
| American President Lines (APL) | APLU | CMA CGM / Ocean Alliance | | | | | Not Started | Not Started |
| ANL | | CMA CGM / Ocean Alliance | | | | | Not Started | Not Started |
| Atlantic Container Line (ACL) | ACLU | Grimaldi Group | | | | | Not Started | Not Started |
| Bahri | | Independent | | | | | Not Started | Not Started |
| CMA CGM | CMDU | CMA CGM / Ocean Alliance | | | | | Not Started | Not Started |
| Cosco Container Lines (COSCO) | COSU | COSCO Group / Ocean Alliance | | | | | Not Started | Not Started |
| Crowley | CMCU | Independent | | | | | Not Started | Not Started |
| CSAV | | Hapag-Lloyd | | | | | Not Started | Not Started |
| CU Lines | | Independent | | | | | Not Started | Not Started |
| Dole | | Independent | | | | | Not Started | Not Started |
| Eimskip | EIMU | Independent | | | | | Not Started | Not Started |
| Emirates Shipping Line (ESL) | | Independent | | | | | Not Started | Not Started |
| FESCO Transportation Group | | Independent | | | | | Not Started | Not Started |
| G2 Ocean | | Independent | | | | | Not Started | Not Started |
| Great White Fleet | | Independent | | | | | Not Started | Not Started |
| Hamburg Süd | SUDU | Maersk | | | | | Not Started | Not Started |
| Hyundai Merchant Marine (HMM) | HDMU | HMM / THE Alliance | | | | | Not Started | Not Started |
| Independent Container Line (ICL) | | Independent | | | | | Not Started | Not Started |
| Linea Peninsular | | Independent | | | | | Not Started | Not Started |
| Marfret Compagnie Maritime | | Independent | | | | | Not Started | Not Started |
| Maruba | | Independent | | | | | Not Started | Not Started |
| Matson | MATS | Independent | | | | | Not Started | Not Started |
| Ocean Network Express (ONE) | ONEY | ONE / THE Alliance | | | | | Not Started | Not Started |
| Orient Overseas Container Line (OOCL) | OOLU | COSCO Group / Ocean Alliance | | | | | Not Started | Not Started |
| Pasha Hawaii Transport Lines | | Independent | | | | | Not Started | Not Started |
| PT. Andalan Pacific Samudra | | Independent | | | | | Not Started | Not Started |
| Safmarine | SAFM | Maersk | | | | | Not Started | Not Started |
| SeaLand | SEJJ | Maersk | | | | | Not Started | Not Started |
| SeaLead | | Independent | | | | | Not Started | Not Started |
| Seaboard Marine | SEAL | Independent | | | | | Not Started | Not Started |
| Shipping Corporation of India | SCIU | Independent | | | | | Not Started | Not Started |
| SM Line (Shipping Maestro) | SMLM | Independent | | | | | Not Started | Not Started |
| Swire Shipping (Polynesia Line) | | Independent | | | | | Not Started | Not Started |
| Swire Shipping (Westwood) | | Independent | | | | | Not Started | Not Started |
| T. S. Lines | | Independent | | | | | Not Started | Not Started |
| Trailer Bridge | | Independent | | | | | Not Started | Not Started |
| Tropical Shipping | | Independent | | | | | Not Started | Not Started |
| Turkon America Line | | Independent | | | | | Not Started | Not Started |
| Wallenius Wilhelmsen Logistics | WLWH | Independent | | | | | Not Started | Not Started |
| Wan Hai | WHLC | Independent | | | | | Not Started | Not Started |
| WECO Shipping | | Independent | | | | | Not Started | Not Started |
| Yang Ming Line | YMLU | Yang Ming / THE Alliance | | | | | Not Started | Not Started |
| ZIM Integrated Shipping Services | ZIMU | ZIM / Independent | | | | | Not Started | Not Started |
