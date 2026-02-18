# Bahri -- Coverage Matrix Row

> Single row for `docs/carrier-field-coverage-matrix.md`. Icons: ✅ Available, ⚠️ Available with caveats, ❓ Unknown, ❌ Unavailable

| Carrier | Schedules | P2P Routes | Booking | Ship. Instr. | Rates | ERD | EFC | DCO | VCO | FCO | LCO | Ctr Assigned | Empty Gate-Out | Full Gate-In | Loaded on Vessel | Disch. @ T/S | Loaded @ T/S | Disch. @ Dest | Full Gate-Out | Empty Returned | ETD | ETA | Arrival @ T/S | Depart @ T/S | Webhooks | Polling | PLN→EST→ACT | Retracted Events | Change Remarks | Delay Codes |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| **Bahri** | ⚠️ | ⚠️ | ❌ | ❌ | ⚠️ | ❓ | ❓ | ❓ | ❓ | ❓ | ❓ | ❓ | ❓ | ❓ | ⚠️ | ❓ | ❓ | ⚠️ | ❓ | ❓ | ⚠️ | ⚠️ | ❓ | ❓ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ |

## Icon Rationale

- **Schedules**: ⚠️ -- Available on web portal (Master Schedule, Voyage Schedules pages) but no API endpoint exists
- **P2P Routes**: ⚠️ -- Fixed routes (US trade, Europe trade) available on website Service Line page but no API endpoint
- **Booking**: ❌ -- No API or known EDI for booking; handled through agents and portal
- **Ship. Instr.**: ❌ -- No API for shipping instructions; handled through agents and portal
- **Rates**: ⚠️ -- Quote request form on web portal and direct contact only; no API
- **ERD through LCO (cutoffs)**: ❓ -- No public documentation; may be communicated through booking agents
- **Ctr Assigned**: ❓ -- Not confirmed from public documentation
- **Empty Gate-Out**: ❓ -- May be available via web portal tracking (login required) but unconfirmed
- **Full Gate-In**: ❓ -- May be available via web portal tracking (login required) but unconfirmed
- **Loaded on Vessel**: ⚠️ -- Load events likely visible on web portal and via third-party tracking (ShipsGo)
- **Disch. @ T/S**: ❓ -- Bahri operates mostly direct services with limited transshipment
- **Loaded @ T/S**: ❓ -- Same as above; limited transshipment in Bahri's liner network
- **Disch. @ Dest**: ⚠️ -- Discharge events likely visible on web portal
- **Full Gate-Out**: ❓ -- May be visible on web portal but unconfirmed
- **Empty Returned**: ❓ -- Unknown availability
- **ETD**: ⚠️ -- Available on web portal (Voyage Schedules, Vessel Tracking) and via ShipsGo; no direct API
- **ETA**: ⚠️ -- Available on web portal and via ShipsGo; no direct API
- **Arrival/Depart @ T/S**: ❓ -- Bahri's liner service is mostly direct; transshipment visibility unknown
- **Webhooks**: ❌ -- No webhook/push API available
- **Polling**: ❌ -- No API to poll
- **PLN->EST->ACT**: ❌ -- No API-level event classifier progression
- **Retracted Events**: ❌ -- No API mechanism
- **Change Remarks**: ❌ -- No API mechanism
- **Delay Codes**: ❌ -- No API mechanism
