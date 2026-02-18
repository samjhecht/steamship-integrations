# Emirates Shipping Line (ESL) -- Coverage Row

> For inclusion in `docs/carrier-field-coverage-matrix.md`
> Icons: ✅ Available | 🔐 Requires partner onboarding | ⚠️ Available with caveats | ❓ Unknown | ❌ Unavailable

| Carrier | Schedules | P2P Routes | Booking | Ship. Instr. | Rates | ERD | EFC | DCO | VCO | FCO | LCO | Ctr Assigned | Empty Gate-Out | Full Gate-In | Loaded on Vessel | Disch. @ T/S | Loaded @ T/S | Disch. @ Dest | Full Gate-Out | Empty Returned | ETD | ETA | Arrival @ T/S | Depart @ T/S | Webhooks | Polling | PLN→EST→ACT | Retracted Events | Change Remarks | Delay Codes |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| **ESL** | ❌ | ❌ | ❌ | ❌ | ❌ | ❓ | ❓ | ❓ | ❓ | ❓ | ❓ | ❓ | ❓ | ❓ | ❓ | ❓ | ❓ | ❓ | ❓ | ❓ | ❓ | ❓ | ❓ | ❓ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ |

## Notes

- ESL has **no public API**. All fields marked ❌ for API Domains reflect the absence of programmatic endpoints.
- Fields marked ❓ may be available on the E-Sea web portal or through third-party aggregators (project44, Dockflow) but could not be confirmed via public documentation.
- Change Detection mechanisms are all ❌ as ESL provides no push, polling, or event classification infrastructure.
- **Aggregator path**: project44 and Dockflow provide ESL container tracking. Through these, some container milestones (load, discharge, gate events) and transport events (ETD/ETA) are likely available but are not direct ESL API fields.
