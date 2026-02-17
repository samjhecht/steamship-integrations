# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

**steamship-integrations** is a TypeScript SDK for unified ocean carrier API integration. The client is SSL (Sea Shipping Line), a federally licensed NVOCC (freight forwarder). The SDK fetches shipment tracking data from major ocean carrier APIs and normalizes it to a common DCSA-based data model.

**Current state:** Phase 1 (carrier API audit) is complete. Phase 2 (SDK implementation) has not started -- there is no `package.json`, `tsconfig.json`, or source code yet. The codebase is documentation and research artifacts.

## Build / Test / Lint Commands

No build toolchain exists yet. When Phase 2 begins, the project will be TypeScript with TDD (test-first), 80%+ coverage target, and pre-commit/pre-push git hooks (see `.wrangler/DEFINITION_OF_DONE.md`).

## Architecture (Planned SDK)

```
Consumer
  -> CarrierTrackingClient (public API: track, getEvents, getCutoffs, getCarrierCapabilities)
    -> Carrier Adapter Registry
      -> Carrier Adapters (one per carrier, implements CarrierAdapter interface)
        -> Normalizer (carrier events -> canonical DCSA model)
          -> Carrier HTTP APIs (fetch/axios, no carrier SDKs)
```

Key interfaces: `CarrierTrackingClient`, `TrackingRequest`, `ShipmentState`, `CarrierAdapter`, `CarrierCapabilities`. Full spec at `.wrangler/specifications/steamship-line-api-audit.md`.

## Domain Knowledge

### DCSA Standards
All field mapping uses DCSA (Digital Container Shipping Association) as the canonical model:
- **Equipment events:** GTOT, GTIN, LOAD, DISC
- **Transport events:** ARRI, DEPA
- **Classifiers:** PLN (planned), EST (estimated), ACT (actual)
- **Cutoff codes:** ECP, EFC, DCO, VCO, FCO, LCO
- **Timestamps:** ISO 8601 with timezone

### Carrier Identifiers (SCAC Codes)
| Carrier | SCAC | API Type | Auth |
|---------|------|----------|------|
| Maersk | MAEU | DCSA full suite | OAuth2 + API Key (dual, path-dependent) |
| MSC | MSCU | DCSA (partner-gated) | Partner onboarding required |
| Hapag-Lloyd | HLCU | DCSA (beta) | IBM API Connect API Key |
| Evergreen | EGLV | DCSA full suite | JWT (Client ID/Secret) |
| Grimaldi | GRIM | Proprietary (GNET WebAPI) | Bearer Token (UserID+UserSecret) |

### Carrier Adapter Design Notes
- DCSA-compliant carriers (Maersk, MSC, Hapag-Lloyd) should share a DCSA base adapter class with thin config layers
- Non-compliant carriers (Grimaldi) get fully custom adapters
- Maersk has dual auth: OAuth2 for `/synergy/` and `/v1/` paths, API Key Consumer-Key header for `/track-and-trace/` and `/schedules/` paths
- Use `fetch`/axios directly -- no carrier-provided SDKs

## Key Documentation

- `docs/required-data-fields.md` -- canonical field reference (all milestones, DCSA event codes)
- `docs/steamship-lines.md` -- carrier index (~45 carriers)
- `docs/carrier-field-coverage-matrix.md` -- cross-carrier field coverage for 5 priority carriers
- `docs/carriers/{maersk,msc,hapag-lloyd,evergreen,grimaldi}/api-inventory.md` -- per-carrier API research
- `.wrangler/specifications/steamship-line-api-audit.md` -- full project specification
- `.wrangler/memos/context-on-existing-state-for-rate-requests.md` -- SSL/Exportfile business context
- `.wrangler/skills/inventorying-carrier-apis/` -- agent skill for researching new carrier APIs

## Scope Boundaries

**In scope (v1):** Read-only tracking, event normalization, cutoff data, schedule info, change detection support
**Out of scope (v1):** Booking creation/mutation, rate/quoting APIs (separate project), service deployment/infra, notification delivery, credential procurement

## Wrangler Project

This is a wrangler-enabled project. Use `.wrangler/issues/`, `.wrangler/memos/`, `.wrangler/specifications/` for project artifacts. Do not create analysis or documentation files at project root.
