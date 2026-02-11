# Open-Source Landscape Research: Ocean Carrier Tracking SDKs

**Date**: 2026-02-11
**Purpose**: Assess whether existing open-source projects overlap with what we're building (a TypeScript SDK that unifies ocean carrier tracking APIs with DCSA normalization).

---

## TL;DR

**No open-source project exists that does what we're building.** The space is wide open. The closest analog is a dead Python scraping library with 7 carriers and no DCSA normalization. No TypeScript/JavaScript SDK for ocean carrier API integration exists in any form. Commercial aggregators charge $1.25-$10+ per container/B/L. Our SDK would be the first open-source, standards-based alternative.

---

## 1. Existing Open-Source Projects

### Direct Competitors (None Viable)

| Project | Language | Carriers | Status | DCSA? | Relevance |
|---------|----------|----------|--------|-------|-----------|
| [dhruvkar/tracktrace](https://github.com/dhruvkar/tracktrace) | Python | 7 (scraping) | Inactive/abandoned | No | Closest match but dead, scraping-based, no Maersk |
| [karrioapi/karrio](https://github.com/karrioapi/karrio) | Python/TS | 30+ parcel only | Active (686 stars) | No | Wrong domain -- parcel (FedEx/UPS/DHL), not ocean |
| [shlee322/delivery-tracker](https://github.com/shlee322/delivery-tracker) | TypeScript | Domestic parcel | Active (667 stars) | No | Wrong domain -- domestic delivery, not ocean |

**tracktrace** is the only project that touches ocean carriers at all. It supports: APL, CMA CGM, Hamburg SUD, Hapag-Lloyd, MSC, ONE, Alianca. Notably missing: Maersk, Evergreen, ZIM, COSCO, Yang Ming. Uses web scraping (BeautifulSoup), not carrier APIs. Ad-hoc data model (simple dict), no standards alignment. MIT license, 50 stars, effectively dead.

### DCSA Ecosystem (Specs + Java Reference Implementations)

The DCSA org (github.com/dcsaorg, 37 repos) provides OpenAPI specs and Java server-side reference implementations. **No client SDKs in any language.**

| Resource | Stars | Usefulness |
|----------|-------|------------|
| [DCSA-OpenAPI](https://github.com/dcsaorg/DCSA-OpenAPI) | 82 | **Critical** -- source YAML specs for generating our TypeScript types |
| [DCSA-Information-Model](https://github.com/dcsaorg/DCSA-Information-Model) | 23 | **High** -- canonical entity relationships (SQL schemas) |
| [DCSA-TNT](https://github.com/dcsaorg/DCSA-TNT) | 18 | Moderate -- Java reference impl, useful for understanding behavior |
| [Conformance-Gateway](https://github.com/dcsaorg/Conformance-Gateway) | 3 | Moderate -- test scenarios usable as fixtures |

**Current DCSA API versions:**
- Track & Trace: v2.2.0 (stable), v3.0.0-Beta-1 (major restructuring, carriers transitioning)
- Booking: v2.0 (Feb 2025)
- Commercial Schedules: v1.0 (Sept 2024)

### Individual Carrier API Wrappers

**Nearly nonexistent.** No major carrier publishes an official SDK (except COSCO).

| Carrier | Open-Source Wrapper? | Official API Portal |
|---------|---------------------|-------------------|
| Maersk | None | [developer.maersk.com](https://developer.maersk.com) -- well-documented |
| MSC | Only in tracktrace (scraping) | [Azure-based portal](https://ovhweportalapim.developer.azure-api.net/) |
| Hapag-Lloyd | Only in tracktrace (scraping) | [api-portal.hlag.com](https://api-portal.hlag.com/) -- well-documented |
| CMA CGM | Only in tracktrace (scraping) | [api-portal.cma-cgm.com](https://api-portal.cma-cgm.com/) -- DCSA-compliant |
| ONE | Only in tracktrace (scraping) | [developers.one-line.com](https://developers.one-line.com/) |
| ZIM | None | Contact-only API access |
| Evergreen | None | [ShipmentLink API Portal](https://www.shipmentlink.com/_ec/APIPORTAL_Home) |
| COSCO | **[cop-cos/COP](https://github.com/cop-cos/COP)** (53 stars, Java/Python, Apache-2.0) | Official open API platform |
| Grimaldi | None | No known public API |
| Yang Ming | None | No known public API portal |

**COSCO's COP** is the standout -- it's the only carrier with an official open-source API client. Provides cargo tracking, schedule inquiries, booking confirmation, waybill management. Java/Python, HMAC-SHA1 auth, 1,000 req/day free trial.

### Useful Utilities

| Project | Language | Purpose |
|---------|----------|---------|
| [kamauwashington/ISO-6346](https://github.com/kamauwashington/ISO-6346) | TypeScript | Container number validation (checksum) |
| [SCAC codes gist](https://gist.github.com/cl3mcg/be760cb4a5751b6546dbec) | JSON | 93 ocean carrier SCAC codes |
| [aisstream.io](https://github.com/aisstream/aisstream) | Multi (incl. TS) | Free AIS vessel position WebSocket API |
| [Flexport SDK](https://github.com/distributeaid/flexport-sdk-js) | TypeScript | Community Flexport API client -- reference for TS SDK patterns |

---

## 2. Commercial Tracking Aggregators

Every commercial aggregator is closed-source. None provide their carrier adapter logic or normalization code as open source.

### API-First Aggregators (Most Relevant Comparisons)

| Service | Carrier Coverage | Pricing | Free Tier? | Key Feature |
|---------|-----------------|---------|------------|-------------|
| [Vizion](https://vizionapi.com) | 99% of ocean freight | $5/container | 15 containers/month | Push-based webhooks, 60+ standardized milestones |
| [Terminal49](https://terminal49.com) | 98% global | $10/container | 100 containers (dev plan) | Clean API, good docs, best free tier |
| [Shipsgo](https://shipsgo.com) | 160+ carriers | Credit-based (per shipment) | 3 credits on signup | Pay per shipment, unlimited polling |
| [JSONCargo](https://jsoncargo.com) | 95%+ of lines | From 99 EUR/month flat | Trial at 11 EUR/month | Flat monthly rate, no per-container charge |
| [Visiwise](https://visiwise.co) | Multi-carrier | From $99/month | Free trial | Auto carrier detection from container number |
| [Portcast](https://portcast.io) | Global | Contact sales | No | AI-powered predictive ETAs |
| [SeaRates](https://searates.com) | 97% worldwide | Per-container | 5/month (registered) | Part of broader freight marketplace |
| [Sinay](https://sinay.ai) | 180+ carriers | Credit-based | Free sandbox | CO2 emissions tracking, port intelligence |
| [TRADLINX](https://tradlinx.com) | Global | $1.25/B/L | No | Cheapest per-B/L pricing |
| [GoComet](https://gocomet.com) | Multi-carrier | Per user/month | Free basic tracking | Broader logistics platform |

### Enterprise Platforms

| Service | Pricing | Notes |
|---------|---------|-------|
| [project44](https://project44.com) | ~$3/container, $6,250+/month minimum | Largest platform, multi-modal |
| [FourKites](https://fourkites.com) | Enterprise custom | Strong in trucking |

### Complementary (AIS/Vessel)

| Service | Type | Notes |
|---------|------|-------|
| [MarineTraffic/Kpler](https://marinetraffic.com) | AIS + container tracking | Pay-per-use, acquired by Kpler |
| [VesselFinder](https://vesselfinder.com) | AIS + container tracking | Has [open-source PHP/Python wrapper](https://github.com/VesselFinder/container-tracking-api-wrapper) |

---

## 3. DCSA Adoption Status Among Carriers

All 10 DCSA member carriers (~75% of global container trade) are implementing DCSA standards:

| Carrier | DCSA Member | TNT API Status |
|---------|------------|----------------|
| MSC | Founder (2019) | Implementing |
| Maersk | Founder (2019) | In production |
| CMA CGM | Founder (2019) | In production (DCSA-compliant) |
| Hapag-Lloyd | Founder (2019) | Implementing |
| ONE | Founder (2019) | Implementing |
| Evergreen | Founder (2019) | Implementing |
| Yang Ming | Founder (2019) | Implementing |
| HMM | Founder (2019) | Implementing |
| ZIM | Founder (2019) | Implementing |
| PIL | Joined 2024 | Implementing |

- 5 carriers already have TNT API in production/testing
- All members expected to be technically ready by 2026
- DCSA+ Partnership Program (launched March 2025) extends to tech providers, freight forwarders, cargo owners

---

## 4. Key Takeaways for Our Project

### The gap is real and large
- No TypeScript/JavaScript SDK for ocean carrier APIs exists
- No open-source project normalizes carrier data to DCSA standards
- No multi-carrier ocean tracking library is actively maintained in any language
- Commercial alternatives charge $1.25-$10+ per container

### Recommended approach informed by this research

1. **Generate TypeScript types from DCSA OpenAPI specs** -- Use `openapi-typescript` against `dcsaorg/DCSA-OpenAPI` YAML files (TNT v2.2.0 for stable, v3.0 for forward-looking)
2. **Study the DCSA Information Model** SQL schemas for entity relationships
3. **Reference Vizion's DCSA mapping docs** at [docs.vizionapi.com/docs/dcsa-milestones](https://docs.vizionapi.com/docs/dcsa-milestones) for practical carrier-to-DCSA event mapping
4. **Consider auto carrier detection** from container number prefix (like Visiwise does)
5. **Support both DCSA-compliant and proprietary carrier APIs** -- DCSA carriers get simpler adapters
6. **Use COSCO's COP as reference** -- only carrier with an official open-source API client

### Design considerations from commercial landscape
- All competitors support: container number, B/L, and booking number as identifiers
- Push/webhook support is expected (Vizion, Terminal49, Visiwise all offer it)
- Top 10-15 carriers cover ~85% of global container traffic
- SCAC codes are the standard carrier identification scheme
- Automatic carrier detection from container number prefix is a valued feature

---

## Sources

### Open-Source Projects
- [dhruvkar/tracktrace](https://github.com/dhruvkar/tracktrace) -- Python ocean carrier tracking (inactive)
- [karrioapi/karrio](https://github.com/karrioapi/karrio) -- Multi-carrier parcel shipping SDK
- [cop-cos/COP](https://github.com/cop-cos/COP) -- COSCO official API platform
- [dcsaorg/DCSA-OpenAPI](https://github.com/dcsaorg/DCSA-OpenAPI) -- DCSA OpenAPI specifications
- [dcsaorg/DCSA-TNT](https://github.com/dcsaorg/DCSA-TNT) -- DCSA Track & Trace reference impl
- [dcsaorg/DCSA-Information-Model](https://github.com/dcsaorg/DCSA-Information-Model) -- DCSA data model
- [distributeaid/flexport-sdk-js](https://github.com/distributeaid/flexport-sdk-js) -- Community Flexport TS SDK
- [VesselFinder/container-tracking-api-wrapper](https://github.com/VesselFinder/container-tracking-api-wrapper) -- VesselFinder API wrapper
- [aisstream/aisstream](https://github.com/aisstream/aisstream) -- Free AIS WebSocket API

### Commercial Platforms
- [Vizion API](https://www.vizionapi.com/)
- [Terminal49](https://terminal49.com/)
- [Shipsgo](https://shipsgo.com/)
- [JSONCargo](https://jsoncargo.com/)
- [Visiwise](https://www.visiwise.co/)
- [Portcast](https://www.portcast.io/)
- [SeaRates](https://www.searates.com/)
- [project44](https://www.project44.com/)

### Carrier Developer Portals
- [Maersk Developer Portal](https://developer.maersk.com)
- [Hapag-Lloyd API Portal](https://api-portal.hlag.com/)
- [CMA CGM API Portal](https://api-portal.cma-cgm.com/)
- [ONE Developer Portal](https://developers.one-line.com/)
- [Evergreen ShipmentLink API](https://www.shipmentlink.com/_ec/APIPORTAL_Home)

### Standards
- [DCSA Standards](https://dcsa.org/standards/)
- [Vizion DCSA Milestones Reference](https://docs.vizionapi.com/docs/dcsa-milestones)
- [OpenFreight API](https://github.com/openfreight-org/api)
