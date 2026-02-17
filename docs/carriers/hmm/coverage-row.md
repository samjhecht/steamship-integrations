# HMM -- Coverage Matrix Row

> Single row for `docs/carrier-field-coverage-matrix.md`. Icons: ✅ Available, 🔐 Requires partner onboarding, ⚠️ Available with caveats, ❓ Unknown, ❌ Unavailable

| Carrier | Schedules | P2P Routes | Booking | Ship. Instr. | Rates | ERD | EFC | DCO | VCO | FCO | LCO | Ctr Assigned | Empty Gate-Out | Full Gate-In | Loaded on Vessel | Disch. @ T/S | Loaded @ T/S | Disch. @ Dest | Full Gate-Out | Empty Returned | ETD | ETA | Arrival @ T/S | Depart @ T/S | Webhooks | Polling | PLN→EST→ACT | Retracted Events | Change Remarks | Delay Codes |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| **HMM** | ✅ | ⚠️ | ⚠️ | ⚠️ | ❌ | ❓ | ❓ | ❓ | ❓ | ❓ | ❓ | ⚠️ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ❌ | ❓ | ✅ | ❓ | ❓ | ❓ |

## Icon Rationale

- **Schedules**: ✅ -- Vessel Schedule and By Calling Port Schedule APIs are live
- **P2P Routes**: ⚠️ -- Port-to-Port Schedule API available but rate-limited to 300 calls/hour
- **Booking**: ⚠️ -- Piloted via DCSA standards (Nov 2025) but not yet on public API portal
- **Ship. Instr.**: ⚠️ -- Piloted via DCSA BL standard (Nov 2025) but not yet on public API portal
- **Rates**: ❌ -- No rates API found
- **ERD through LCO (cutoffs)**: ❓ -- May exist in schedule/booking responses but unconfirmed without live access
- **Ctr Assigned**: ⚠️ -- Inferred from first equipment event, not explicitly reported
- **Empty Gate-Out through Empty Returned (milestones)**: ✅ -- DCSA TNT equipment events
- **ETD/ETA**: ✅ -- Available via TNT transport events and schedule APIs
- **Arrival/Depart @ T/S**: ✅ -- Transport events at intermediate ports
- **Webhooks**: ❌ -- No webhook/push subscription endpoint found
- **Polling**: ❓ -- TNT endpoint exists but timestamp-based filtering not confirmed
- **PLN->EST->ACT**: ✅ -- DCSA transport event classifier codes supported
- **Retracted Events / Change Remarks / Delay Codes**: ❓ -- Unknown, not confirmed in available documentation
