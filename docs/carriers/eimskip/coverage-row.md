# Eimskip -- Coverage Row

> For inclusion in `docs/carrier-field-coverage-matrix.md`
> Icons: ✅ Available | 🔐 Requires partner onboarding | ⚠️ Available with caveats | ❓ Unknown | ❌ Unavailable

| Carrier | Schedules | P2P Routes | Booking | Ship. Instr. | Rates | ERD | EFC | DCO | VCO | FCO | LCO | Ctr Assigned | Empty Gate-Out | Full Gate-In | Loaded on Vessel | Disch. @ T/S | Loaded @ T/S | Disch. @ Dest | Full Gate-Out | Empty Returned | ETD | ETA | Arrival @ T/S | Depart @ T/S | Webhooks | Polling | PLN>EST>ACT | Retracted Events | Change Remarks | Delay Codes |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| **Eimskip** | ✅ | ✅ | ❌ | ❌ | ❌ | ❓ | ❓ | ❓ | ❓ | ❓ | ❓ | ❓ | ❓ | ❓ | ❓ | ❓ | ❓ | ❓ | ❓ | ❓ | ⚠️ | ⚠️ | ⚠️ | ❓ | ❌ | ❓ | ❌ | ❌ | ❌ | ❌ |

## Coverage Notes

- **Schedules / P2P Routes**: Available via `api/VesselSchedule` endpoints with route-based and point-to-point search
- **ETD**: Marked as caveat (⚠️) -- only ArrivalDate is documented per port; departure must be inferred from schedule patterns
- **ETA**: Marked as caveat (⚠️) -- ArrivalDate is available per port but only as planned schedule data, no EST/ACT progression
- **Arrival @ T/S**: Marked as caveat (⚠️) -- available for intermediate ports in schedule data, but only planned values
- **Booking / Ship. Instr. / Rates**: No API endpoints exist for these functions
- **Cutoffs (ERD through LCO)**: All unknown -- the CargoStatus endpoint may return cutoff data but its response is undocumented
- **Container Milestones (3.x)**: All unknown -- CargoStatus endpoint exists but response structure is completely undocumented
- **Webhooks**: No webhook support documented
- **PLN>EST>ACT / Retracted Events / Change Remarks / Delay Codes**: No evidence of any change detection mechanisms
