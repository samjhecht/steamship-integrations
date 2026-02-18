# Dole Ocean Cargo Express -- Coverage Matrix Row

> Single row for `docs/carrier-field-coverage-matrix.md`. Icons: ✅ Available, 🔐 Requires partner onboarding, ⚠️ Available with caveats, ❓ Unknown, ❌ Unavailable

| Carrier | Schedules | P2P Routes | Booking | Ship. Instr. | Rates | ERD | EFC | DCO | VCO | FCO | LCO | Ctr Assigned | Empty Gate-Out | Full Gate-In | Loaded on Vessel | Disch. @ T/S | Loaded @ T/S | Disch. @ Dest | Full Gate-Out | Empty Returned | ETD | ETA | Arrival @ T/S | Depart @ T/S | Webhooks | Polling | PLN→EST→ACT | Retracted Events | Change Remarks | Delay Codes |
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
| **Dole** | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ |

## Icon Rationale

- **All fields: ❌** -- Dole Ocean Cargo Express does not expose any public API. All data is accessible only through their Salesforce Experience Cloud portal (doleoceancargo.com), which is not designed for programmatic consumption.
- **Schedules**: Published sailing schedules exist on the public website but only as rendered HTML -- no API endpoint.
- **Booking**: Booking management exists in the private customer portal but only as a Salesforce web application -- no API.
- **Rates**: Rate requests are submitted via web form only -- no programmatic access.
- **Container milestones (Loaded on Vessel, Disch. @ Dest)**: The portal shows "Container Traceability" and "Traceability Activity" pages with milestone events (confirmed: "VPF - Discharged from vessel"), but these are rendered Salesforce pages, not API responses.
- **ETD/ETA**: Available on published schedule pages and booking details in the portal, but not via API.
- **Webhooks/Polling/Change Detection**: No mechanisms available -- no API exists.
- **DCSA**: Not a DCSA member. No DCSA implementation.

> **Note**: If Salesforce Connected App API access were negotiated directly with DOCE, many of these fields could potentially become available (the underlying data likely exists in Salesforce objects). Without API access, programmatic coverage is 0%.
