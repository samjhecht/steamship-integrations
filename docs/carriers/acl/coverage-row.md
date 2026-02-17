# ACL (Atlantic Container Line) -- Coverage Row

> For inclusion in `docs/carrier-field-coverage-matrix.md`
> Icons: ✅ Available | 🔐 Requires partner onboarding | ⚠️ Available with caveats | ❓ Unknown | ❌ Unavailable

| Carrier | Schedules | P2P Routes | Booking | Ship. Instr. | Rates | ERD | EFC | DCO | VCO | FCO | LCO | Ctr Assigned | Empty Gate-Out | Full Gate-In | Loaded on Vessel | Disch. @ T/S | Loaded @ T/S | Disch. @ Dest | Full Gate-Out | Empty Returned | ETD | ETA | Arrival @ T/S | Depart @ T/S | Webhooks | Polling | PLN→EST→ACT | Retracted Events | Change Remarks | Delay Codes |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| **ACL** | ⚠️ | ⚠️ | 🔐 | 🔐 | ❌ | ❓ | ❓ | ❓ | ❓ | ❓ | ❓ | ❓ | 🔐 | 🔐 | 🔐 | ❌ | ❌ | 🔐 | 🔐 | 🔐 | ⚠️ | ⚠️ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ |

## Icon Justifications

| Field | Icon | Rationale |
|-------|------|-----------|
| Schedules | ⚠️ | Available on MyACL portal (web only); no API. Fixed weekly rotation is predictable |
| P2P Routes | ⚠️ | Available on portal; limited relevance due to fixed route |
| Booking | 🔐 | Available via EDI (IFTMBF/IFTMBC) -- requires Trading Partner Agreement |
| Ship. Instr. | 🔐 | Available via EDI (IFTMIN) -- requires Trading Partner Agreement |
| Rates | ❌ | No rate API or digital rate access |
| ERD | ❓ | Unknown -- not confirmed in any digital channel |
| EFC | ❓ | Unknown |
| DCO | ❓ | Unknown |
| VCO | ❓ | Unknown |
| FCO | ❓ | Unknown |
| LCO | ❓ | Unknown |
| Ctr Assigned | ❓ | Unknown -- not visible in public documentation |
| Empty Gate-Out | 🔐 | Available via EDI (CODECO/322) -- requires Trading Partner Agreement. Also via aggregators |
| Full Gate-In | 🔐 | Same as above |
| Loaded on Vessel | 🔐 | Available via EDI (IFTSTA/315). Also via aggregators |
| Disch. @ T/S | ❌ | ACL operates direct services; transshipment is rare/not applicable |
| Loaded @ T/S | ❌ | Same as above |
| Disch. @ Dest | 🔐 | Available via EDI (IFTSTA/315). Also via aggregators |
| Full Gate-Out | 🔐 | Available via EDI (CODECO/322). Also via aggregators |
| Empty Returned | 🔐 | Available via EDI (CODECO/322). Also via aggregators |
| ETD | ⚠️ | Available on MyACL portal and via aggregators; no direct API |
| ETA | ⚠️ | Available via EDI (IFTMAN/312), portal, and aggregators |
| Arrival @ T/S | ❌ | Direct service, minimal transshipment |
| Depart @ T/S | ❌ | Direct service, minimal transshipment |
| Webhooks | ❌ | No webhook capability (no API). Aggregators (Terminal49) offer webhooks as proxy |
| Polling | ❌ | No API to poll |
| PLN→EST→ACT | ❌ | No DCSA event classifier model |
| Retracted Events | ❌ | No retraction mechanism |
| Change Remarks | ❌ | No structured change remarks |
| Delay Codes | ❌ | No standardized delay reason codes |
