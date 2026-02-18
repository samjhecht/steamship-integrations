# G2 Ocean -- Coverage Matrix Row

> Single row for `docs/carrier-field-coverage-matrix.md`. Icons: ✅ Available, 🔐 Requires partner onboarding, ⚠️ Available with caveats, ❓ Unknown, ❌ Unavailable

| Carrier | Schedules | P2P Routes | Booking | Ship. Instr. | Rates | ERD | EFC | DCO | VCO | FCO | LCO | Ctr Assigned | Empty Gate-Out | Full Gate-In | Loaded on Vessel | Disch. @ T/S | Loaded @ T/S | Disch. @ Dest | Full Gate-Out | Empty Returned | ETD | ETA | Arrival @ T/S | Depart @ T/S | Webhooks | Polling | PLN→EST→ACT | Retracted Events | Change Remarks | Delay Codes |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| **G2 Ocean** | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ |

## Icon Rationale

- **Schedules**: ❌ -- No public API. Sailing schedules exist in MyG2 portal but require login and have no programmatic access
- **P2P Routes**: ❌ -- No public API. Trade route info on website/MyG2 but no API
- **Booking**: ❌ -- No public API. MyG2 mentions booking management and "integration capability" but nothing is publicly documented
- **Ship. Instr.**: ❌ -- No public API. OceanDocs handles documentation but has no API. CargoX eBL pilot was conducted in 2019
- **Rates**: ❌ -- No rate API. Rates obtained via commercial negotiations only
- **ERD through LCO (cutoffs)**: ❌ -- Not applicable. G2 Ocean is a breakbulk/open hatch carrier, not a container liner. Container-specific cutoff codes do not apply
- **Ctr Assigned through Empty Returned (milestones)**: ❌ -- Not applicable. Breakbulk cargo is not containerized; container equipment events do not apply
- **ETD/ETA**: ❌ -- No public API. ETD/ETA data exists in MyG2 portal and via AIS-based third-party trackers (Shipsgo), but no programmatic carrier API access
- **Arrival/Depart @ T/S**: ❌ -- No public API. Breakbulk transshipment is uncommon for G2 Ocean
- **Webhooks**: ❌ -- No public API, no webhook capability
- **Polling**: ❌ -- No public API to poll
- **PLN->EST->ACT**: ❌ -- No DCSA implementation. DCSA standards are designed for containerized shipping
- **Retracted Events / Change Remarks / Delay Codes**: ❌ -- No event API

## Notes

G2 Ocean is a breakbulk/open hatch carrier, not a container liner. DCSA standards and container-specific tracking fields do not apply to their primary cargo types. The carrier has no public API; all digital access is through the MyG2 customer portal (requires business relationship). Not a viable candidate for the SDK's container-focused tracking adapter pattern.
