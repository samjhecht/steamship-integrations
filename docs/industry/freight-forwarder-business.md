# The Freight Forwarding Business Model

A deep dive into how ocean freight forwarders operate, structured for evaluating SaaS product opportunities in carrier API integration.

---

## 1. What a Freight Forwarder Actually Does

A freight forwarder is an intermediary that arranges the movement of goods on behalf of shippers (exporters/importers) without owning the ships, trucks, or planes that carry the cargo. The forwarder acts as a logistics orchestrator -- negotiating rates with ocean carriers, booking cargo space, preparing customs and shipping documentation, coordinating inland transport, and tracking shipments through to delivery.

The core value proposition is expertise and consolidation:

- **Carrier relationships**: Forwarders negotiate volume-based rates across dozens of carriers, passing savings to customers who lack the scale to negotiate directly.
- **Regulatory knowledge**: International shipping involves customs declarations, hazardous goods regulations, trade compliance, and country-specific documentation. Forwarders handle this complexity.
- **Single point of contact**: Rather than coordinating separately with a carrier, a customs broker, a trucker, and a warehouse, the shipper deals with one forwarder.
- **Risk management**: Forwarders advise on cargo insurance, Incoterms, and routing decisions that affect delivery reliability and cost.

The global freight forwarding market was valued at approximately USD 337 billion in 2026 and is projected to grow at a 5-6% CAGR through 2031. The industry is highly fragmented -- the top seven players (Kuehne+Nagel, DHL Global Forwarding, DSV, DB Schenker, Bollore Logistics, Nippon Express, and CEVA Logistics) collectively hold only about 30% market share, with thousands of small and mid-sized forwarders making up the rest.

## 2. How They Make Money

Freight forwarders operate on an **arbitrage model**: they purchase carrier capacity at negotiated rates and sell it to customers at a markup, keeping the spread. This is supplemented by fees for value-added services.

### Revenue Streams

| Revenue Source | Description | Typical Contribution |
|---|---|---|
| **Freight margin** | Difference between carrier rate paid and rate charged to customer | 60-75% of gross profit |
| **Documentation fees** | Bill of lading preparation, customs filing, certificates of origin | 10-15% of gross profit |
| **Customs brokerage** | Licensed customs clearance services (separate FMC license in US) | 5-15% of gross profit |
| **Ancillary services** | Cargo insurance arrangement, warehousing coordination, trucking | 5-15% of gross profit |
| **Surcharge pass-through** | Fuel surcharges, peak season surcharges (often marked up slightly) | Variable |

### Margin Structure

Freight forwarding is a low-margin, high-volume business. Carriers consume 62-85% of gross revenue as cost of goods sold. Of the remainder, forwarders typically convert 20-30% to EBIT, yielding overall **EBIT margins of 1-11%** depending on scale, market conditions, and operational efficiency.

The margin environment has been tightening. A 2025 industry survey found that 92% of forwarding agents worldwide expect margins to compress further in 2026, driven by overcapacity in carrier fleets and the normalization of freight rates after the pandemic-era spike. The days of inflated spot rates delivering easy profits are over -- growth now depends on operational efficiency, technology adoption, and customer retention.

### How Rates Work in Practice

Using SSL (Sea Shipping Line) as a concrete example: a customer quote is assembled from two components:

1. **Carrier rate** -- the all-in rate from the ocean carrier, which includes the base rate plus 20+ standard surcharges (fuel, port fees, Red Sea piracy surcharge, etc.). Carriers send rate updates to forwarders, typically as PDFs, at least weekly.
2. **Forwarder margin** -- the fee SSL adds on top, negotiated per-customer and sometimes per-route. A sales rep agrees on the margin with each customer, and it gets stored in their backend system.

The final customer quote = carrier rate + forwarder margin + applicable service fees.

## 3. NVOCC vs. Freight Broker vs. 3PL

These terms are often confused. The distinctions matter for understanding regulatory requirements and competitive positioning.

| Dimension | NVOCC | Freight Forwarder (OTI) | Freight Broker | 3PL |
|---|---|---|---|---|
| **Definition** | Operates as a carrier without owning vessels; buys space from ocean carriers and resells it | Arranges transport on behalf of shippers across carriers and modes | Connects shippers with carriers; does not handle cargo or documentation | Outsourced logistics provider offering bundled services |
| **Issues own Bill of Lading** | Yes (house B/L) | No (uses carrier B/L) | No | Varies |
| **Carrier liability** | Takes on carrier-level responsibility for cargo | Acts as agent, not carrier | No liability for cargo | Varies by contract |
| **FMC license required (US)** | Yes -- OTI license + $75,000 surety bond | Yes -- OTI license + $50,000 surety bond | No FMC license (unless ocean) | No FMC license required |
| **Typical services** | Ocean freight, consolidation (LCL), B/L issuance | Booking, documentation, customs, multi-modal coordination | Matching shipper to carrier | Warehousing, fulfillment, freight, distribution |

**Key distinction for our context**: SSL is a federally licensed NVOCC. This means SSL can issue its own house bills of lading, consolidate less-than-container-load (LCL) shipments, and assume carrier-level responsibility for the cargo. This is a higher level of authority (and liability) than a basic freight forwarder or broker. NVOCCs typically maintain deeper carrier relationships and handle higher volumes.

A 3PL (third-party logistics provider) is a broader category that may encompass freight forwarding, warehousing, fulfillment, and distribution. Large 3PLs like DHL Supply Chain and XPO Logistics offer freight forwarding as one service among many. The term "3PL" is more common in domestic logistics; in ocean freight, "forwarder" and "NVOCC" are the operative terms.

## 4. Operational Workflow: End-to-End

The following diagram shows the standard lifecycle of a freight forwarding engagement from initial customer inquiry through post-delivery invoicing.

```
                          FREIGHT FORWARDER OPERATIONAL WORKFLOW
                          ======================================

  CUSTOMER                    FORWARDER                         CARRIER
  --------                    ---------                         -------

  1. QUOTE REQUEST
  [Email/Portal/Phone] -----> [Receive inquiry]
                               |
                               v
                              [Look up carrier rates]
                              [Calculate margin]
                              [Generate quote] ----------> 2. QUOTE DELIVERY
                               |                           [Send to customer]
                               v
  3. BOOKING                  [Customer accepts]
  [Confirm booking] <-------- [Confirm with customer]
                               |
                               v
                              [Submit booking] ------------> [Carrier confirms]
                              [Receive booking ref]  <------ [Booking number]
                               |
  4. DOCUMENTATION             v
                              [Prepare shipping docs]
                              [Bill of Lading]
                              [Commercial Invoice]
                              [Packing List]
                              [Customs Declaration]
                              [Submit to customs] ---------> [Customs clearance]
                               |
  5. SHIPMENT EXECUTION        v
                              [Coordinate cargo pickup]
                              [Deliver to port/terminal]
                              [Container loading] ---------> [Vessel departure]
                               |
  6. TRACKING & MONITORING     v
                              [Monitor carrier portals]
                              [Track milestones]
                              [Watch cutoff deadlines]
  [Status updates] <--------- [Notify customer]
                               |
  7. EXCEPTION MANAGEMENT      v
                              [Handle delays/rollovers]
                              [Rebook if needed]
                              [Manage demurrage risk]
                               |
  8. DELIVERY                  v
                              [Coordinate last-mile] <------ [Vessel arrival]
                              [Customs clearance at dest]
                              [Arrange trucking]
  [Cargo received] <--------- [Confirm delivery]
                               |
  9. INVOICING & SETTLEMENT    v
                              [Reconcile charges]
                              [Generate invoice]
  [Pay invoice] <------------- [Send to customer]
                              [Pay carrier] ---------------> [Carrier paid]
```

### What Happens at Each Step

**1. Quote Request**: Customer sends an inquiry specifying cargo type, weight/dimensions, origin, destination, Incoterms, and desired dates. Arrives via email (most common), phone, website form, or self-serve portal.

**2. Rate Lookup and Quote Generation**: The forwarder looks up current carrier rates for the requested route (from their rate database, carrier portals, or rate management system), applies their margin, adds service fees, and generates a formatted quote. At SSL, this involves logging into the Exportfile portal, inputting parameters, and generating the quote. Industry-wide, roughly 90% of shippers now expect digital quote turnaround in under two hours, but the average forwarder response time for spot quotes is 67 hours.

**3. Booking**: Once the customer accepts, the forwarder submits a booking request to the selected carrier -- via the carrier's portal, EDI, or API. The carrier confirms with a booking reference number and allocated space.

**4. Documentation**: The forwarder prepares or collects: the bill of lading (B/L), commercial invoice, packing list, certificate of origin, customs declarations, and any cargo-specific documents (dangerous goods declarations, fumigation certificates). For an NVOCC like SSL, they issue their own house B/L.

**5. Shipment Execution**: Coordinate cargo pickup from the shipper's facility, delivery to the port terminal, and container loading. The forwarder monitors cutoff deadlines -- the carrier-imposed deadlines for documentation submission, container delivery to the terminal, and hazmat declarations.

**6. Tracking and Monitoring**: Once the vessel departs, the forwarder monitors shipment progress via carrier portals, EDI messages, or API feeds. They track milestones: gate-in at origin terminal, vessel departure, transshipment events, vessel arrival at destination, container discharge, and gate-out.

**7. Exception Management**: Delays, port congestion, vessel rollovers (cargo bumped to a later sailing), and customs holds all require intervention. The forwarder rebooks, reroutes, or communicates revised ETAs to the customer.

**8. Delivery Coordination**: At the destination port, the forwarder arranges customs clearance, pays duties on behalf of the customer (if terms require it), and coordinates trucking or rail to the final destination.

**9. Invoicing**: The forwarder reconciles all charges (carrier costs, port fees, trucking, customs duties, their own service fees and margin), generates a customer invoice, and settles with the carrier and other vendors.

## 5. The Scale Spectrum

Operations differ dramatically by company size. The following table characterizes the four main tiers:

| Dimension | Micro (1-5 people) | Small (5-50 people) | Mid-Market (50-500 people) | Enterprise (500+ people) |
|---|---|---|---|---|
| **Monthly volume** | <100 containers | 100-1,000 containers | 1,000-10,000 containers | 10,000+ containers |
| **Example** | Solo operator, niche trade lane | SSL (Sea Shipping Line) | Flexport, Seko Logistics | Kuehne+Nagel, DHL GF, DSV |
| **Rate management** | Carrier PDFs and Excel spreadsheets | Custom database (like Exportfile) or basic TMS | Dedicated RMS (Freightos, WebCargo) integrated with TMS | Enterprise rate platforms, direct carrier EDI/API feeds |
| **Quoting process** | Manual: read PDF, build quote in Excel, email to customer | Semi-manual: portal-based with backend database, some automation | TMS-integrated: rates pulled automatically, quotes generated in system | Fully automated: API-driven rate retrieval, instant quoting portals |
| **Tracking** | Log into each carrier portal individually, update spreadsheets | Mix of portal checks and email alerts; customer updates via email/phone | TMS with multi-carrier tracking dashboard; some API feeds | Integrated platform with real-time carrier API connections, customer portal |
| **Booking** | Carrier portals, email | Carrier portals, some EDI | TMS-to-carrier EDI or API | Full EDI/API integration, automated booking |
| **Documentation** | Manual preparation in Word/PDF | Template-based, partially automated | TMS-generated with compliance checks | Automated document generation and e-filing |
| **Customer communication** | Phone and email | Email, basic portal (like Exportfile's customer portal) | Customer-facing portal with self-serve tracking | White-labeled platform, mobile app, real-time notifications |
| **Staff per shipment** | High (1 person handles everything) | Moderate (specialized roles: sales, ops, docs) | Lower (automation handles routine tasks) | Lowest (technology handles most routine operations) |
| **Technology spend** | <$500/month | $1,000-5,000/month | $10,000-100,000/month | $1M+/month |
| **Key pain point** | Everything is manual; can't scale | Systems exist but don't talk to each other; lots of re-keying | Integration complexity; carrier connectivity gaps | Legacy system modernization; global consistency |

### Where Most Forwarders Sit

The industry is heavily weighted toward the micro and small tiers. While enterprise players dominate by volume, the vast majority of the estimated 100,000+ freight forwarding companies globally are small operations running lean teams with limited technology. More than 62% of SME forwarders still depend on manual freight documentation processes.

## 6. Technology Stack

A typical mid-market freight forwarder operates the following systems:

### Core Systems

| System | Purpose | Major Products | Notes |
|---|---|---|---|
| **TMS (Transportation Management System)** | Central operational platform: bookings, documentation, tracking, billing | CargoWise (WiseTech Global), Magaya, Descartes GLN, GoFreight, Softship | CargoWise dominates the mid-to-enterprise tier but faces pushback on pricing. DSV is migrating from CargoWise to DB Schenker's Tango TMS. |
| **Rate Management System (RMS)** | Store, update, and query carrier rates for quoting | WebCargo (by Freightos), Catapult (by Magaya), Freightos, Wisor | The fastest-growing category. Many forwarders still use spreadsheets. AI-powered systems can now extract rates from PDFs automatically. |
| **Customer Portal** | Self-serve quote requests, tracking, document access for customers | Logixboard, Portrix, built-in TMS portals | Increasingly expected by customers. 46% of shippers have switched forwarders for better technology. |
| **Accounting / ERP** | Financial management, invoicing, accounts payable/receivable | QuickBooks, Xero (small), SAP, NetSuite (mid/enterprise) | Must integrate with TMS for automated invoicing. |
| **CRM** | Customer relationship and sales pipeline management | Salesforce, HubSpot, built-in TMS modules | Often underinvested at smaller forwarders. |

### Carrier Connectivity Layer

This is the layer most relevant to the steamship-integrations project. Today, forwarders connect to ocean carriers through a hierarchy of methods:

| Method | Description | Adoption | Pros | Cons |
|---|---|---|---|---|
| **Manual portals** | Log into each carrier's website, search by container/B/L number | Universal (100%) | No setup cost, always available | Extremely labor-intensive at scale; no automation possible |
| **EDI (via Inttra/E2open)** | Structured electronic messages (EDIFACT/ANSI X12) via intermediary platforms | Common at mid-market+ (~40-50% of ocean volume) | Standardized, reliable, mature | Expensive, slow to set up, batch-oriented (not real-time), rigid schemas |
| **Carrier APIs (DCSA-aligned)** | Direct REST API calls to carrier systems; increasingly DCSA-standardized | Early adoption (~15-20% of forwarders) | Real-time, flexible, modern, lower ongoing cost | Requires development resources; each carrier API is different; authentication varies |
| **Aggregator platforms** | Third-party services that aggregate carrier data via API/scraping | Growing (project44, FourKites, Vizion) | One integration covers many carriers | Additional cost; data freshness varies; limited to tracking (usually) |

DCSA (Digital Container Shipping Association) is driving API standardization. MSC adopted the DCSA Commercial Schedules API in 2025, and carriers like Maersk, Hapag-Lloyd, and Evergreen already offer DCSA-aligned track-and-trace APIs. However, adoption remains uneven -- many carriers still rely heavily on legacy EDI, and even "DCSA-compliant" APIs vary in implementation details.

## 7. Where Carrier API Integration Fits

Programmatic carrier access would improve forwarder operations at five specific touchpoints:

### 7.1 Automated Tracking

**Current state**: Operations staff log into 5-15 different carrier portals daily, searching by container number or B/L to check shipment status. For a forwarder handling 500 active shipments across 10 carriers, this can consume 2-4 hours of staff time daily.

**With API integration**: A unified tracking system polls carrier APIs on a schedule (or receives webhook events), normalizing status data into a single dashboard. Staff see all shipments in one view with automated exception alerts.

**Business impact**: Reduces tracking labor by 70-80%. Enables proactive customer communication instead of reactive "let me check on that."

### 7.2 Cutoff Monitoring

**Current state**: Cutoff deadlines (documentation cutoff, cargo cutoff, VGM cutoff) are buried in carrier portals or booking confirmations. Missing a cutoff means the container gets rolled to the next sailing, incurring delays and potential demurrage charges. Nine major carriers collected approximately $12.9 billion in demurrage and detention charges between April 2020 and March 2023.

**With API integration**: Automated monitoring of cutoff times with escalating alerts as deadlines approach. System flags at-risk shipments before it's too late.

**Business impact**: Directly reduces demurrage/detention costs and prevents customer-facing delays.

### 7.3 Rate and Schedule Lookups

**Current state**: Carrier rates arrive as PDFs or spreadsheets. Someone manually transcribes them into the rate database (at SSL, the Exportfile team in the Philippines does this). Schedule lookups require visiting carrier websites. Sales teams lose an estimated 43% of their time on manual quoting tasks, and quote response times average 67 hours industry-wide.

**With API integration**: Automated rate ingestion and schedule queries. Quoting systems pull live rates and available sailings directly from carrier APIs.

**Business impact**: Quote response time drops from hours/days to minutes. Eliminates transcription errors (carrier invoices carry a 15% error rate when managed manually).

### 7.4 Booking Status Updates

**Current state**: After submitting a booking, the forwarder checks the carrier portal for confirmation, amendment requests, or rejections. Updates are communicated to customers manually.

**With API integration**: Booking status changes flow automatically into the TMS and trigger customer notifications.

**Business impact**: Faster customer communication, reduced staff workload on status inquiries.

### 7.5 Document Retrieval

**Current state**: Bills of lading, arrival notices, and other carrier-issued documents are downloaded from carrier portals and manually attached to shipment records.

**With API integration**: Documents are automatically retrieved and linked to shipment records in the TMS.

**Business impact**: Reduced document handling time, lower risk of missing or misfiled documents.

## 8. Common Pain Points

The following pain points are most acute at the small-to-mid-market tier (5-500 people) -- the segment most likely to adopt a carrier API integration product:

| Pain Point | Description | Impact |
|---|---|---|
| **Multi-portal tracking** | Staff manually check 5-15 carrier websites daily for shipment updates | 2-4 hours/day of labor per operations person; delayed customer updates |
| **Rate management from PDFs** | Carrier rate sheets arrive as PDFs/Excel; must be manually transcribed into rate databases | Transcription errors, stale rates, slow quoting (67-hour average response time) |
| **Cutoff monitoring** | Deadlines for documentation and cargo delivery are tracked on spreadsheets or memory | Missed cutoffs lead to container rollovers, demurrage charges ($thousands per incident) |
| **Fragmented systems** | TMS, rate database, customer portal, accounting -- none of them talk to each other natively | Re-keying data across systems; inconsistency; staff frustration |
| **Customer communication delays** | Customers ask "where's my container?" and staff must manually check before responding | Poor customer experience; 46% of shippers switch forwarders for better technology |
| **Document handling** | Bills of lading, customs docs, and certificates are created, shared, and filed manually | Compliance risk, misfiled documents, wasted staff time |
| **Demurrage and detention** | Charges for containers sitting at port or held beyond free time | Billions in industry-wide costs; individual incidents can cost $1,000-10,000+ |
| **Carrier data inconsistency** | Each carrier uses different terminology, event codes, and data formats | Impossible to build a unified view without normalization; staff must learn each carrier's system |

## 9. Digital Transformation: Where the Industry Stands

### Current State (2026)

The freight forwarding industry is in the early-to-middle stages of digital transformation, with a stark divide between enterprise players and everyone else:

- **Enterprise forwarders** (Kuehne+Nagel, DHL GF, DSV) have built or acquired proprietary platforms with real-time carrier integration, customer portals, and AI-powered operations. DSV's migration from CargoWise to Tango illustrates the scale of investment at this tier.
- **Mid-market forwarders** are adopting cloud TMS platforms and beginning to explore API-based carrier connectivity. Many are evaluating or deploying rate management systems (WebCargo, Wisor, Catapult) as a first step.
- **Small forwarders** (the majority) remain dependent on carrier portals, spreadsheets, email, and phone. More than 62% still rely on manual documentation processes. Technology adoption is limited by cost, IT expertise, and change resistance (33% of organizations report internal resistance as a barrier).
- **SME share of digital platforms** is growing -- SMEs now represent 70% of digital freight forwarding platform users, driven by falling costs and low-code onboarding tools.

### What's Driving Change

1. **Customer expectations**: Shippers increasingly expect real-time tracking, instant quoting, and self-serve portals. The Amazon effect has reached B2B logistics.
2. **Carrier API availability**: DCSA standardization and carrier investment in developer portals are making programmatic access feasible for the first time.
3. **Competitive pressure from digital-native forwarders**: Flexport, Forto, and Zencargo demonstrated that technology-first logistics is viable. While Flexport's valuation has declined and the "digital vs. traditional" gap has narrowed, the expectation bar has been permanently raised.
4. **Margin compression**: With 92% of forwarders expecting further margin tightening in 2026, technology-driven efficiency is no longer optional -- it's a survival strategy. Forwarders who adopt AI and automation can handle more shipments without proportional headcount growth.
5. **Market consolidation**: WiseTech Global's $2.1 billion acquisition of E2open signals that the infrastructure layer is consolidating, creating both opportunity (better tools) and risk (vendor lock-in) for forwarders.

### Where It's Headed

The freight forwarding software market is projected to grow from USD 14.3 billion (2025) to USD 22.2 billion (2030). The digital freight forwarding market in the US alone is expected to reach USD 9.8 billion in 2026, growing at 19% CAGR. API integration development attracts 44% of technology spending in the sector, reflecting where forwarders see the highest ROI.

The trajectory is clear: forwarders who do not digitize their carrier connectivity, quoting, and tracking workflows within the next 3-5 years will face existential competitive pressure. This creates a window for SaaS products that can bridge the gap between carrier APIs and forwarder operational systems -- particularly for the small-to-mid-market segment that lacks the engineering resources to build integrations in-house.

---

## Sources

- [McKinsey -- Freight forwarders' earnings amid carrier-rate volatility](https://www.mckinsey.com/industries/logistics/our-insights/freight-forwarders-earnings-amid-carrier-rate-volatility)
- [CargoForwarder Global -- Forwarders expect margins to tighten further in 2026](https://cargoforwarder.eu/2025/12/21/forwarders-expect-margins-to-tighten-further-in-2026/)
- [GoFreight -- Freight Forwarding Industry Outlook and Predictions for 2026](https://gofreight.com/blog/freight-forwarding-industry-outlook)
- [GoFreight -- How to Increase Freight Forwarding Profit Margins](https://gofreight.com/blog/education/how-to-increase-freight-forwarding-profit-margins.html)
- [Mordor Intelligence -- Freight Forwarding Market Size, Trends, Share Report 2031](https://www.mordorintelligence.com/industry-reports/freight-forwarding-market)
- [Mordor Intelligence -- Digital Freight Forwarder Market Analysis](https://www.mordorintelligence.com/industry-reports/digital-freight-forwarding-market)
- [Mordor Intelligence -- US Digital Freight Forwarding Market](https://marksparksolutions.com/reports/us-digital-freight-forwarding-market)
- [Fortune Business Insights -- Freight Forwarding Market Size, Share and Trends 2032](https://www.fortunebusinessinsights.com/freight-forwarding-market-114883)
- [DCSA -- The Path to Visibility in Container Shipping](https://dcsa.org/newsroom/resources/the-path-to-visibility-in-container-shipping/)
- [DCSA -- The Benefits of Replacing EDI with APIs in Container Shipping](https://dcsa.org/newsroom/the-benefits-of-replacing-edi-with-apis-in-container-shipping)
- [Smart Maritime Network -- MSC adopts DCSA API for standardised schedule data exchange](https://smartmaritimenetwork.com/2025/06/30/msc-adopts-dcsa-api-for-standardised-schedule-data-exchange/)
- [Container Management -- DSV begins shift from CargoWise to Tango TMS](https://container-mag.com/2026/02/19/dsv-begins-migrating-from-cargowise-to-db-schenkers-tango-system/)
- [The Loadstar -- Forwarders are rethinking CargoWise](https://theloadstar.com/forwarders-are-rethinking-cargowise-what-are-their-options/)
- [Wisor -- Freight Rate Management in 2026](https://wisor.ai/freight-rate-management/)
- [Wisor -- Top 8 TMS Platforms for Freight Forwarders in 2026](https://wisor.ai/best-tms-for-freight-forwarders/)
- [Magaya -- Rate Management Software: Your Top Questions Answered](https://www.magaya.com/rate-management-software-your-top-questions-answered/)
- [GoFreight -- End-to-End Shipment Management Guide](https://gofreight.com/blog/education/end-to-end-shipment-management)
- [AltexSoft -- Freight Forwarder: Key Workflows and Technology](https://www.altexsoft.com/blog/freight-forwarder/)
- [GoFreight -- Demurrage Charges: Every Freight Forwarder's Nightmare](https://gofreight.com/blog/freights-snap/detention-and-demurrage-charges.html)
- [FreightTracker -- Demurrage and Detention](https://freighttrackertms.com/solutions/demurrage-and-detention/)
- [Contrary Research -- Flexport Business Breakdown](https://research.contrary.com/company/flexport)
- [The Loadstar -- Struggle for digital forwarders](https://theloadstar.com/its-a-struggle-for-digital-forwarders-with-poster-boy-flexport-losing-value/)
- [Logixboard -- 8 Biggest Challenges and Solutions for Freight Forwarders](https://logixboard.com/logistics-business/3pl-resources/8-biggest-challenges-and-solutions-for-freight-forwarders/)
- [Carbon6 -- Freight Forwarder vs Broker vs 3PL](https://www.carbon6.io/blog/freight-forwarder-vs-broker-vs-3pl-whats-the-difference/)
- [Shipping Solutions -- NVOCC vs. Freight Forwarder](https://shippingsolutionssoftware.com/blog/nvocc-vs-freight-forwarder)
- [Holland and Knight -- Forwarding and Logistics Startups Must Be Wary of US Licensing](https://www.hklaw.com/en/insights/publications/2020/06/forwarding-and-logistics-startups-must-be-wary-of-us-licensing)
- [Freightify -- 10 Reasons Why Digitalization is Important for Logistics](https://freightify.com/blog/10-reasons-for-digitization)
- [Markets and Markets -- Transportation Management System Market Report 2025](https://www.marketsandmarkets.com/Market-Reports/transportation-management-market-232446179.html)
