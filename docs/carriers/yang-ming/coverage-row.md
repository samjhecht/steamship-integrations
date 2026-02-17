# Yang Ming Line -- Coverage Row

> Single table row matching `docs/carrier-field-coverage-matrix.md` column order.
> Icons: ✅ Available | 🔐 Requires partner onboarding | ⚠️ Available with caveats | ❓ Unknown | ❌ Unavailable

| Carrier | Schedules | P2P Routes | Booking | Ship. Instr. | Rates | ERD | EFC | DCO | VCO | FCO | LCO | Ctr Assigned | Empty Gate-Out | Full Gate-In | Loaded on Vessel | Disch. @ T/S | Loaded @ T/S | Disch. @ Dest | Full Gate-Out | Empty Returned | ETD | ETA | Arrival @ T/S | Depart @ T/S | Webhooks | Polling | PLN→EST→ACT | Retracted Events | Change Remarks | Delay Codes |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| **Yang Ming** | ⚠️ | ⚠️ | ❌ | ❌ | ❌ | ❓ | ❓ | ❓ | ❓ | ❓ | ❓ | ⚠️ | 🔐 | 🔐 | 🔐 | 🔐 | 🔐 | 🔐 | 🔐 | 🔐 | 🔐 | 🔐 | 🔐 | 🔐 | ❓ | ❓ | 🔐 | ❓ | ❓ | ❓ |

## Icon Rationale

- **Schedules / P2P Routes**: ⚠️ -- Available on web portal (HTML/CSV/XML download) but no programmatic API confirmed
- **Booking / Ship. Instr. / Rates**: ❌ -- No API available; booking/SI only through web portal or EDI
- **Cutoffs (ERD-LCO)**: ❓ -- DCSA TNT v2.2 spec supports cutoffs, but Yang Ming's implementation not verified
- **Container Milestones (3.2-3.9)**: 🔐 -- DCSA TNT v2.2 API confirmed, but requires partner onboarding (manual "Apply API" process)
- **Container Assigned (3.1)**: ⚠️ -- Inferred from first equipment event, not explicitly reported
- **Transport Events (ETD/ETA/T-S)**: 🔐 -- Same as milestones; available via DCSA TNT v2.2 after onboarding
- **Webhooks**: ❓ -- DCSA v2.2 spec includes Subscription Callback API; Yang Ming implementation unknown
- **Polling**: ❓ -- DCSA v2.2 mandates polling support; Yang Ming implementation unknown
- **PLN->EST->ACT**: 🔐 -- Standard DCSA classifier progression; available after onboarding
- **Retracted Events / Change Remarks / Delay Codes**: ❓ -- DCSA v2.2 spec supports these; Yang Ming implementation unknown
