# Specification: Carrier Tracking SDK & API Audit

> **Status**: Draft
> **Author**: Sam (spec owner), agent (drafter)
> **Date**: 2026-02-11
> **Project**: steamship-integrations

---

## Executive Summary

**What:** A TypeScript SDK that provides a unified interface for fetching shipment tracking data from ocean carrier APIs. Given a shipment identifier and carrier, the SDK returns standardized tracking data — container milestones, schedule updates, cutoff deadlines, and booking status — normalized to a common data model regardless of the underlying carrier API.

**Why:** SSL's operations team currently monitors shipment status by manually logging into individual carrier web portals. This is time-consuming, error-prone, and makes it impossible to proactively detect schedule changes and notify downstream clients. A programmatic SDK enables automation of shipment tracking, change detection, and downstream notifications.

**Approach:** Audit-first. Before writing any code, we will conduct a comprehensive parallel audit of carrier APIs to understand exactly what data is available, how to access it, and what constraints exist. The audit findings directly inform the SDK's interface design, carrier adapter implementations, and the realistic scope of what can be automated.

**Scope:**
- **Included:** Carrier API research/audit, SDK library design and implementation, carrier adapter implementations for priority carriers, documentation
- **Excluded:** Service wrapper / deployment infrastructure (future), integration into SSL's existing .NET/Azure systems (SSL's decision), rate/quoting APIs (separate project), booking creation (read-only tracking focus for v1)

---

## Goals and Non-Goals

### Goals

1. **Complete carrier API intelligence** — Produce a thorough, verified inventory of what data each priority carrier exposes, through what mechanisms, with what constraints
2. **Unified tracking interface** — A single SDK call that returns normalized shipment tracking data regardless of which carrier is involved
3. **Coverage of SSL's required data fields** — All fields documented in `docs/required-data-fields.md` are represented in the SDK's data model, with per-carrier availability clearly documented
4. **Change detection support** — The SDK's data model makes it straightforward for consumers to detect when milestones or dates have changed between polls
5. **Extensible carrier support** — Adding a new carrier should be a well-defined, repeatable process (documented pattern + agent workflow)

### Non-Goals

- **Service deployment** — The SDK is a library; wrapping it in a service is a future concern
- **SSL system integration** — We build a great SDK; SSL decides how to consume it (direct import, service wrapper, or language migration)
- **Rate/quoting APIs** — Handled by a separate project (see `.wrangler/memos/context-on-existing-state-for-rate-requests.md`)
- **Booking creation/mutation** — v1 is read-only tracking; write operations (create booking, submit SI) are future scope
- **Notification delivery** — The SDK surfaces data and changes; the notification system that acts on them is the consumer's responsibility
- **Credential procurement** — The SDK assumes credentials are provided by the consumer; we document what's needed but don't handle carrier onboarding

---

## Background & Context

### Problem Statement

SSL is a federally licensed NVOCC (freight forwarder). After a shipment is booked with an ocean carrier, SSL's team must manually monitor the carrier's web portal for status updates — vessel ETAs, container milestones, cutoff changes — and relay those updates to their downstream customers. With dozens of active shipments across multiple carriers, this manual process doesn't scale and introduces delays in communicating critical changes.

### Industry Context

The ocean shipping industry is in the middle of a standardization push led by DCSA (Digital Container Shipping Association). Major carriers (Maersk, MSC, CMA CGM, Hapag-Lloyd, ONE, ZIM) are adopting DCSA's Track & Trace, Booking, and Commercial Schedules APIs. This is good news — it means a significant subset of carriers share common event codes, data models, and API patterns. However:

- Not all carriers are DCSA members (Grimaldi, Evergreen have varying levels of adoption)
- Even DCSA-compliant carriers may implement different API versions or subsets
- Some carriers have proprietary APIs that predate DCSA
- Some carriers have no public API at all

The audit phase is specifically designed to map this landscape before we commit to an architecture.

### Current State

- `docs/required-data-fields.md` — Structured reference of all fields SSL needs, with DCSA event code mappings verified against Maersk's API
- `docs/steamship-lines.md` — Index of ~45 carriers (API docs fields currently empty — the audit will populate this)
- `docs/skills/inventorying-provider-data/` — Research skill template (needs customization for freight/carrier context before use)
- No code exists yet

### Proposed State

After this project:

```
steamship-integrations/
├── docs/
│   ├── required-data-fields.md          # ✅ Done — canonical field reference
│   ├── steamship-lines.md               # Populated with API links, status for all carriers
│   ├── skills/
│   │   └── inventorying-carrier-apis/   # Customized skill for carrier research
│   │       ├── SKILL.md
│   │       └── templates/
│   └── carriers/
│       ├── maersk/
│       │   └── data-availability-inventory.md
│       ├── msc/
│       │   └── data-availability-inventory.md
│       ├── hapag-lloyd/
│       │   └── data-availability-inventory.md
│       ├── grimaldi/
│       │   └── data-availability-inventory.md
│       └── evergreen/
│           └── data-availability-inventory.md
├── src/
│   ├── index.ts                         # Public API
│   ├── types.ts                         # Core data model (events, milestones, etc.)
│   ├── client.ts                        # Main SDK client
│   ├── carriers/
│   │   ├── base.ts                      # Base carrier adapter interface
│   │   ├── maersk/
│   │   ├── msc/
│   │   ├── hapag-lloyd/
│   │   ├── grimaldi/
│   │   └── evergreen/
│   └── utils/
├── tests/
└── package.json
```

---

## Requirements

### Functional Requirements

#### SDK Core

- **FR-001:** SDK MUST accept a shipment identifier (booking reference, Bill of Lading number, or container number) and carrier identifier, and return the current tracking state for that shipment
- **FR-002:** SDK MUST normalize all carrier-specific responses into a common data model based on DCSA event codes (as documented in `docs/required-data-fields.md`)
- **FR-003:** SDK MUST support all data field categories: API domains (schedules, booking status), cutoffs (ERD, DCO, VCO, FCO, etc.), container milestones (gate-out through empty return), and transport events (ETD/ETA)
- **FR-004:** SDK MUST clearly indicate which fields are available vs. unavailable for a given carrier (not all carriers expose all fields)
- **FR-005:** SDK MUST support looking up shipments by at least: booking reference, Bill of Lading number, and container number (where the carrier supports each)
- **FR-006:** SDK MUST expose event classifier progression (Planned → Estimated → Actual) so consumers can detect schedule changes
- **FR-007:** SDK MUST return timestamps in a consistent format (ISO 8601 with timezone) regardless of carrier API format

#### Carrier Adapters

- **FR-008:** Each carrier adapter MUST implement a common interface defined in the base adapter
- **FR-009:** Each carrier adapter MUST handle the carrier's specific authentication mechanism (OAuth2, API key, token, etc.) given credentials provided by the consumer
- **FR-010:** Each carrier adapter MUST handle pagination transparently — the consumer gets complete results
- **FR-011:** Each carrier adapter MUST map carrier-specific event types/codes to the canonical DCSA-based model
- **FR-012:** Each carrier adapter MUST handle rate limiting gracefully (respect limits, retry with backoff, surface rate-limit errors clearly)

#### Data Model

- **FR-013:** The SDK's shipment tracking response MUST include: current milestone (where in the lifecycle), all known milestones with timestamps, all known cutoffs with deadlines, vessel/voyage info, route with ports, and container details
- **FR-014:** Each milestone/event MUST carry metadata: source carrier, timestamp, event classifier (PLN/EST/ACT), and the raw carrier event code alongside the canonical code
- **FR-015:** The response MUST include a per-field availability indicator so consumers know which fields came from the carrier vs. which are unavailable for that carrier

### Non-Functional Requirements

- **Reliability:** SDK MUST handle carrier API downtime gracefully — clear error types, no crashes, partial results where possible
- **Performance:** Individual tracking lookups SHOULD complete in under 5 seconds (network-dependent; SDK should not add meaningful overhead)
- **Extensibility:** Adding a new carrier adapter SHOULD require only implementing the base interface and adding carrier-specific mapping logic — no changes to SDK core
- **Testability:** Each carrier adapter MUST be testable with mocked API responses (recorded fixtures from real API calls where possible)
- **Documentation:** Every public API surface MUST have JSDoc documentation; each carrier adapter MUST reference its corresponding audit document in `docs/carriers/`
- **Zero runtime dependencies on carrier SDKs:** Carrier adapters SHOULD use HTTP directly (fetch/axios) rather than importing carrier-provided SDKs that may be poorly maintained

---

## Architecture

### High-Level Design

```
Consumer (SSL's systems, scripts, services)
    │
    ▼
┌─────────────────────────────────────────┐
│  SDK Public API                         │
│  ┌────────────────────────────────────┐ │
│  │  CarrierTrackingClient             │ │
│  │  - track(carrier, shipmentId)      │ │
│  │  - getEvents(carrier, shipmentId)  │ │
│  │  - getCutoffs(carrier, shipmentId) │ │
│  └────────────┬───────────────────────┘ │
│               │                         │
│  ┌────────────▼───────────────────────┐ │
│  │  Carrier Adapter Registry          │ │
│  │  - resolves carrier → adapter      │ │
│  └────────────┬───────────────────────┘ │
│               │                         │
│  ┌────────────▼───────────────────────┐ │
│  │  Carrier Adapters                  │ │
│  │  ┌─────────┐ ┌─────┐ ┌──────────┐ │ │
│  │  │ Maersk  │ │ MSC │ │ Hapag-LL │ │ │
│  │  └─────────┘ └─────┘ └──────────┘ │ │
│  │  ┌──────────┐ ┌───────────┐       │ │
│  │  │ Grimaldi │ │ Evergreen │       │ │
│  │  └──────────┘ └───────────┘       │ │
│  └────────────────────────────────────┘ │
│               │                         │
│  ┌────────────▼───────────────────────┐ │
│  │  Normalizer                        │ │
│  │  - carrier events → canonical      │ │
│  │  - DCSA passthrough for compliant  │ │
│  │  - custom mapping for proprietary  │ │
│  └────────────────────────────────────┘ │
└─────────────────────────────────────────┘
    │
    ▼
Carrier APIs (Maersk, MSC, Hapag-Lloyd, ...)
```

### Core Interface (Preliminary)

This will be refined after the audit phase reveals the full landscape, but the directional shape:

```typescript
interface CarrierTrackingClient {
  // Primary: get full shipment state
  track(request: TrackingRequest): Promise<ShipmentState>;

  // Granular: get just events/milestones
  getEvents(request: TrackingRequest): Promise<TrackingEvent[]>;

  // Granular: get just cutoffs
  getCutoffs(request: TrackingRequest): Promise<CutoffDeadline[]>;

  // Discovery: what does this carrier support?
  getCarrierCapabilities(carrier: CarrierCode): CarrierCapabilities;
}

interface TrackingRequest {
  carrier: CarrierCode;
  // At least one identifier required
  bookingReference?: string;
  billOfLadingNumber?: string;
  containerNumber?: string;
  // Carrier-specific credentials
  credentials: CarrierCredentials;
}

interface ShipmentState {
  carrier: CarrierCode;
  identifiers: ShipmentIdentifiers;
  currentMilestone: MilestoneCode;
  milestones: Milestone[];
  cutoffs: CutoffDeadline[];
  route: RouteInfo;
  containers: ContainerInfo[];
  vessel: VesselInfo | null;
  metadata: ResponseMetadata;
}

interface Milestone {
  code: CanonicalMilestoneCode;    // Our canonical code (from required-data-fields.md)
  carrierCode: string;              // Raw carrier event code
  classifier: 'PLN' | 'EST' | 'ACT';
  timestamp: string;                // ISO 8601
  location?: LocationInfo;
  vessel?: VesselInfo;
  isTransshipment: boolean;
  raw: unknown;                     // Raw carrier response for debugging
}
```

### Carrier Adapter Pattern

```typescript
interface CarrierAdapter {
  readonly carrierCode: CarrierCode;
  readonly capabilities: CarrierCapabilities;

  authenticate(credentials: CarrierCredentials): Promise<AuthSession>;
  fetchTrackingEvents(session: AuthSession, id: ShipmentIdentifiers): Promise<RawCarrierEvent[]>;
  fetchCutoffs(session: AuthSession, id: ShipmentIdentifiers): Promise<RawCarrierCutoff[]>;
  normalizeEvent(raw: RawCarrierEvent): Milestone;
  normalizeCutoff(raw: RawCarrierCutoff): CutoffDeadline;
}

// Capabilities tell consumers what to expect per carrier
interface CarrierCapabilities {
  supportsBookingRefLookup: boolean;
  supportsBolLookup: boolean;
  supportsContainerLookup: boolean;
  supportsWebhooks: boolean;
  availableMilestones: CanonicalMilestoneCode[];
  availableCutoffs: CutoffCode[];
  authType: 'oauth2' | 'api_key' | 'bearer_token' | 'basic' | 'none';
  dcssCompliant: boolean;
  rateLimits?: RateLimitInfo;
  notes: string[];  // Human-readable gotchas from the audit
}
```

---

## Project Phases

### Phase 1: Carrier API Audit

**Goal:** Produce a complete, verified data availability inventory for each priority carrier before writing any code.

**Deliverables:**
1. Customized `docs/skills/inventorying-carrier-apis/` skill — adapted from the generic provider data inventory skill to freight/carrier context (replace GDPR/OAuth/consumer framing with carrier API/EDI/login-wall framing)
2. Completed inventories in `docs/carriers/<carrier>/data-availability-inventory.md` for all 5 priority carriers
3. Updated `docs/steamship-lines.md` — at minimum the 5 priority carriers populated with API docs URLs and status

**Process:**
1. Customize the inventorying skill (manual, single-agent task)
2. Deploy 5 parallel agent workflows — one per carrier — each following the customized skill to research and document that carrier's API landscape
3. Review and cross-validate the 5 inventories
4. Synthesize findings into a compatibility matrix: which carriers support which fields, through which access methods, with what constraints

**Priority Carriers:**

| Carrier | Expected API Availability | Notes |
|---------|--------------------------|-------|
| **Maersk** | Strong — DCSA founding member, well-documented APIs at developer.maersk.com | Reference carrier; Maersk mappings already in required-data-fields.md |
| **MSC** | Likely — DCSA member, has been building digital platform | Second largest carrier globally |
| **Hapag-Lloyd** | Likely — DCSA member, has developer portal | Strong digital presence |
| **Grimaldi** | Unknown — niche carrier, may not have public API | RoRo/container specialist; may require portal scraping or EDI |
| **Evergreen Line** | Partial — has some tracking capability, DCSA adoption unclear | Large carrier but historically less API-forward |

**Audit Scope per Carrier** (customized skill will formalize this):
- API availability: does a public/partner API exist? documentation quality?
- Authentication: what credentials are needed? is there a developer portal? partner program gate?
- Tracking endpoints: which of our required fields (milestones, cutoffs, schedule) are available?
- Lookup methods: booking ref? B/L? container number? which are supported?
- Rate limits: documented? observed?
- Data format: DCSA-compliant? proprietary JSON? XML? EDI?
- Webhooks/push: supported or polling only?
- Sandbox/test environment: available for development?
- Known gotchas: limitations, beta endpoints, deprecated features
- Login wall / portal-only data: is anything available only through the web portal?

### Phase 2: SDK Foundation

**Goal:** Build the core SDK infrastructure — types, interfaces, client, adapter registry, normalizer — based on audit findings.

**Depends on:** Phase 1 complete (audit findings inform the type system and interface design)

**Deliverables:**
1. TypeScript project setup (package.json, tsconfig, test framework, linting)
2. Core type definitions (`types.ts`) — the canonical data model
3. Base carrier adapter interface (`carriers/base.ts`)
4. Carrier adapter registry and resolution
5. Main client implementation (`client.ts`)
6. Event normalizer (carrier-specific → canonical)
7. Error types and handling patterns
8. Test infrastructure with mock carrier responses

**Key Design Decisions (to be finalized after Phase 1):**
- Whether DCSA-compliant carriers share a base adapter class vs. each getting their own
- How to handle carriers with no API (skip? manual import? scraping? flag as unsupported?)
- Credential storage interface (we define the shape; consumer provides the implementation)
- Whether to support async iteration for large event histories

### Phase 3: Carrier Integrations

**Goal:** Implement carrier adapters for each priority carrier.

**Depends on:** Phase 2 complete (SDK foundation provides the interface to implement against)

**Process:**
- Each carrier integration is an independent, parallelizable work unit
- Each follows the same pattern: implement the adapter interface, write carrier-specific HTTP calls, map responses to canonical model, write tests with recorded fixtures
- Each adapter references its corresponding audit document in `docs/carriers/`

**Deliverables per carrier:**
1. Carrier adapter implementation in `src/carriers/<carrier>/`
2. Response mapping/normalization logic
3. Tests with mocked API responses
4. Documentation of carrier-specific quirks and limitations

**Carrier implementation order** (subject to audit findings):
1. Maersk — best documented, DCSA reference implementation, most API surface
2. MSC — large carrier, likely DCSA-compatible
3. Hapag-Lloyd — likely DCSA-compatible
4. Evergreen — may need more custom work
5. Grimaldi — most uncertain; may need alternative approach

---

## Open Questions & Decisions

### Resolved Decisions

| Decision | Options Considered | Chosen | Rationale | Date |
|----------|-------------------|--------|-----------|------|
| Language | TypeScript, Python, C#/.NET | TypeScript | Both TS and Python are equally non-native to SSL's .NET stack; TS chosen for ecosystem fit. SSL can migrate later if needed | 2026-02-11 |
| Consumption model | Library, microservice, both | Library first | Keep it simple; service wrapper is a future concern | 2026-02-11 |
| Audit before code | Code-first, audit-first | Audit first | Can't design good interfaces without knowing the carrier API landscape | 2026-02-11 |
| SSL integration approach | Tight integration, standalone SDK | Standalone SDK | Too many unknowns about SSL's systems; build a great SDK and let them decide | 2026-02-11 |

### Open Questions

- [ ] **Q1: Carriers without APIs** — What do we do when the audit reveals a carrier has no public API? Options: (a) mark as unsupported, (b) web scraping adapter, (c) EDI integration, (d) manual data entry interface. *Depends on: Phase 1 audit findings, especially for Grimaldi.*

- [ ] **Q2: API credential procurement** — Do the priority carriers require partner agreements, or can anyone register for developer access? *Depends on: Phase 1 audit. Requires SSL input for carriers that need business relationships.*

- [ ] **Q3: Webhook support** — Should the SDK support receiving webhooks (push) in addition to polling, or is polling-only sufficient for v1? *Depends on: Phase 1 audit showing which carriers support push, and whether SSL wants real-time or periodic updates.*

- [ ] **Q4: Caching** — Should the SDK include a caching layer to avoid re-fetching data within a configurable window? Or is that the consumer's responsibility? *Design decision for Phase 2.*

- [ ] **Q5: Historical events vs. current state** — Should `track()` return the full event history or just the current state? Or both via different methods? *Design decision for Phase 2, informed by how much history each carrier API exposes.*

- [ ] **Q6: Grimaldi vs. ACL** — The carrier index lists both "Grimaldi Lines" and "Atlantic Container Line (Grimaldi Lines)". Which entity are we targeting? Are they the same API? *Needs clarification from SSL.*

---

## Risks & Mitigations

| Risk | Probability | Impact | Mitigation |
|------|-------------|--------|------------|
| Carrier has no public API | Medium (Grimaldi) | High — can't automate that carrier | Audit will surface this early; can evaluate scraping, EDI, or marking unsupported |
| Carrier requires partner agreement / business relationship | Medium | Medium — delays credential access | Document requirements in audit; SSL handles business relationships |
| Carrier API is poorly documented | Medium | Medium — slower adapter development | Audit includes testing against live APIs where possible; community libraries as reference |
| Carrier API changes / deprecates endpoints | Low (short-term) | Medium | Pin to specific API versions; document version in adapter; monitor changelogs |
| DCSA compliance varies between carriers | High | Low — expected | Normalizer layer handles mapping; DCSA-compliant carriers get simpler adapters |
| Rate limits too restrictive for operational use | Low | High — can't poll frequently enough | Audit documents rate limits; design polling strategy around actual limits; use webhooks where available |

---

## Success Criteria

### Phase 1 (Audit) Success

- [ ] Customized inventorying skill is ready for deployment
- [ ] All 5 priority carriers have completed, reviewed data availability inventories
- [ ] Each inventory covers: API availability, auth requirements, field coverage, rate limits, sandbox access, gotchas
- [ ] `docs/steamship-lines.md` updated for priority carriers
- [ ] Cross-carrier compatibility matrix produced
- [ ] Open questions about carriers without APIs are surfaced with options

### Phase 2 (SDK Foundation) Success

- [ ] TypeScript project builds and tests pass
- [ ] Core types represent all fields from `docs/required-data-fields.md`
- [ ] Base adapter interface is defined and documented
- [ ] At least one carrier adapter (Maersk) validates the interface design
- [ ] Error handling covers: auth failure, rate limiting, network errors, carrier-specific errors
- [ ] Test infrastructure supports mocked carrier responses

### Phase 3 (Carrier Integrations) Success

- [ ] All carriers with available APIs have working adapters
- [ ] Each adapter passes tests with recorded API response fixtures
- [ ] Each adapter's capabilities accurately reflect what the carrier supports
- [ ] Carrier-specific documentation links back to audit inventory
- [ ] SDK can be `npm install`ed and used to track a shipment with minimal setup

---

## References

### Project Documents

- `docs/required-data-fields.md` — Canonical field reference with DCSA/Maersk mappings
- `docs/steamship-lines.md` — Carrier index
- `docs/skills/inventorying-provider-data/` — Base research skill (to be customized)
- `.wrangler/memos/context-on-existing-state-for-rate-requests.md` — Broader SSL/Exportfile business context

### External Resources

- [DCSA Standards](https://dcsa.org/standards/) — Track & Trace, Booking, Commercial Schedules
- [DCSA OpenAPI specs (GitHub)](https://github.com/dcsaorg/DCSA-OpenAPI) — Machine-readable API definitions
- [Maersk Developer Portal](https://developer.maersk.com) — Reference carrier API implementation
- [DCSA Track & Trace v3](https://app.swaggerhub.com/apis/dcsaorg/DCSA-TNT/3.0.0) — Event model specification

### Glossary

- **SSL** — Sea Shipping Line, the freight forwarding business (our client)
- **NVOCC** — Non-Vessel Operating Common Carrier (SSL's license type)
- **DCSA** — Digital Container Shipping Association (industry standards body)
- **B/L** — Bill of Lading (shipping document / shipment identifier)
- **ERD** — Earliest Release Date (when empty container becomes available)
- **ETD / ETA** — Estimated Time of Departure / Arrival
- **FCL** — Full Container Load
- **LCL** — Less than Container Load
- **VGM** — Verified Gross Mass (SOLAS regulatory requirement)
- **EDI** — Electronic Data Interchange (legacy B2B data format common in shipping)
- **PLN / EST / ACT** — Planned / Estimated / Actual (DCSA event classifier progression)
