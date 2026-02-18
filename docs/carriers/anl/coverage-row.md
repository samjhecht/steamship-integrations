# ANL -- Field Coverage Row

> For inclusion in `docs/carrier-field-coverage-matrix.md`
> Icons: ✅ Available | 🔐 Requires partner onboarding | ⚠️ Available with caveats | ❓ Unknown | ❌ Unavailable
>
> ANL uses CMA CGM's API infrastructure. Coverage is identical to CMA CGM.

| Carrier | Schedules | P2P Routes | Booking | Ship. Instr. | Rates | ERD | EFC | DCO | VCO | FCO | LCO | Ctr Assigned | Empty Gate-Out | Full Gate-In | Loaded on Vessel | Disch. @ T/S | Loaded @ T/S | Disch. @ Dest | Full Gate-Out | Empty Returned | ETD | ETA | Arrival @ T/S | Depart @ T/S | Webhooks | Polling | PLN->EST->ACT | Retracted Events | Change Remarks | Delay Codes |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| **ANL** | ✅ | ✅ | ✅ | ✅ | ❓ | ❓ | ❓ | ⚠️ | ⚠️ | ⚠️ | ❓ | ⚠️ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ✅ | ❓ | ❓ | ❓ |

## Notes

- All access is through CMA CGM's API portal (https://api-portal.cma-cgm.com)
- SCAC: ANNU
- DCSA: TNT v2.2.0 + CS v1.0.7 (PILOT) via CMA CGM
- Auth: Dual (API Key + OAuth2) via CMA CGM
- Webhooks are CMA CGM proprietary (not DCSA subscription model)
- Cutoffs marked partial (⚠️) where CMA CGM Routing Finder mentions them but field-level mapping needs live verification
- Retracted events, change remarks, and delay codes are in the DCSA spec but CMA CGM's implementation is unconfirmed
