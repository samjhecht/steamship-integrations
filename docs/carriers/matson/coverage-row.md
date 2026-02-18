# Matson -- Coverage Row

> For inclusion in `docs/carrier-field-coverage-matrix.md`
> Column order matches the matrix header exactly.
> Icons: ✅ Available | 🔐 Requires partner onboarding | ⚠️ Available with caveats | ❓ Unknown | ❌ Unavailable

| Carrier | Schedules | P2P Routes | Booking | Ship. Instr. | Rates | ERD | EFC | DCO | VCO | FCO | LCO | Ctr Assigned | Empty Gate-Out | Full Gate-In | Loaded on Vessel | Disch. @ T/S | Loaded @ T/S | Disch. @ Dest | Full Gate-Out | Empty Returned | ETD | ETA | Arrival @ T/S | Depart @ T/S | Webhooks | Polling | PLN→EST→ACT | Retracted Events | Change Remarks | Delay Codes |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| **Matson** | ⚠️ | ❌ | ⚠️ | ❓ | ❌ | ❓ | ❓ | ⚠️ | ⚠️ | ⚠️ | ❓ | ❓ | ⚠️ | ⚠️ | ⚠️ | ⚠️ | ⚠️ | ⚠️ | ⚠️ | ⚠️ | ⚠️ | ⚠️ | ❓ | ❓ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ |

> **Notes**: All "Available with caveats" (⚠️) fields are available via web portal and/or EDI (X12 315/322) but NOT via REST/SOAP API. Matson has no public API. Programmatic access requires EDI business relationship or third-party aggregator (Terminal49 confirmed, project44/Vizion likely). Change detection fields are all unavailable because there is no event API, webhook, or DCSA compliance.
