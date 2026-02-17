# Hamburg Süd -- Coverage Row

> For inclusion in `docs/carrier-field-coverage-matrix.md`
> All fields accessed via Maersk's APIs (Hamburg Süd has no independent API)

| Carrier | Schedules | P2P Routes | Booking | Ship. Instr. | Rates | ERD | EFC | DCO | VCO | FCO | LCO | Ctr Assigned | Empty Gate-Out | Full Gate-In | Loaded on Vessel | Disch. @ T/S | Loaded @ T/S | Disch. @ Dest | Full Gate-Out | Empty Returned | ETD | ETA | Arrival @ T/S | Depart @ T/S | Webhooks | Polling | PLN→EST→ACT | Retracted Events | Change Remarks | Delay Codes |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| **Hamburg Süd** | ✅ | ✅ | ✅ | ⚠️ | ⚠️ | ✅ | ✅ | ✅ | ✅ | ✅ | ⚠️ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ |

## Legend

- ✅ Available (via Maersk API)
- ⚠️ Available with caveats:
  - **Ship. Instr.**: eDocumentation implementation not fully confirmed for ex-Hamburg Süd shipments
  - **Rates**: Maersk Offers API `brandScac` support for SUDU not publicly documented
  - **LCO**: Conditional -- only present for CFS (LCL) shipments
