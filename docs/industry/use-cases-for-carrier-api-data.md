# Use Cases for Carrier API Data

This document catalogs the concrete use cases that drive demand for ocean carrier API data. For each use case, it describes who needs it, what data they require, how they currently solve the problem, what is broken about the current approach, and how a modern API integration platform would improve outcomes. The goal is to help evaluate which use cases represent the strongest product opportunities.

---

## 1. Shipment Tracking and Visibility

**Who cares:** Freight forwarders, shippers (BCOs), consignees, 3PLs, supply chain control towers.

**What data they need:** Real-time container location, current transport milestone (gate-in, loaded, departed, arrived, discharged, gate-out), vessel name and voyage number, port of loading and discharge, and transshipment details.

**How they currently solve it:** Operations staff log into individual carrier portals -- often 5 to 15 different websites per day -- to manually check container status. Larger forwarders may receive EDI 315 status messages through Inttra or purchase subscriptions to visibility platforms such as project44, FourKites, or Vizion. Some forwarders maintain spreadsheets updated by hand.

**What is broken:** Manual portal checking is labor-intensive and error-prone. A mid-size forwarder managing 200 active shipments across 8 carriers might spend 2-3 hours daily on status checks alone. Each carrier portal has different terminology, layout, and data granularity. EDI messages arrive with lag and lack the detail available through modern APIs. Visibility platform subscriptions (project44, FourKites) run $50,000-$200,000+ annually and impose their own data model as an intermediary layer.

**Value of API integration:** Automated polling at configurable intervals, normalized to a single DCSA-based data model. One dashboard view across all carriers. The forwarder controls the data pipeline and can build custom logic on top of raw events rather than depending on a vendor's interpretation. Project44 reports tracking across 98%+ of global ocean freight; a purpose-built integration for a forwarder's actual carrier mix can achieve the same coverage at a fraction of the cost.

---

## 2. ETA Prediction and Exception Management

**Who cares:** Freight forwarders, shippers, supply chain planners, warehouse operators, consignees awaiting cargo.

**What data they need:** Carrier-published estimated arrival times (both at intermediate ports and final destination), schedule change notifications, delay reasons, and actual vs. planned timestamps for comparison.

**How they currently solve it:** Carrier portals publish ETAs, but these are often updated without notification. Forwarders check periodically and manually relay updates to customers. Some use AIS (Automatic Identification System) vessel tracking to independently estimate arrival, but AIS provides vessel position, not container-level status.

**What is broken:** Carrier ETAs update silently. A forwarder may quote a customer an arrival date on Monday, only to discover Thursday that the carrier revised the ETA by 5 days. According to Sea-Intelligence, global schedule reliability hovered between 61% and 68% through 2025, dropping to 62.4% in January 2026. That means roughly 4 in 10 vessels arrive outside their scheduled window. Without automated change detection, forwarders are reactive rather than proactive, and their customers bear the downstream consequences: missed warehouse appointments, production line stoppages, and retail stockouts.

**Value of API integration:** Continuous ETA monitoring with change detection. When a carrier revises an ETA, the system can immediately calculate the delta, classify it as minor or significant, and trigger appropriate alerts. Historical ETA data enables the forwarder to build their own predictive models that outperform carrier estimates. Maersk, the most reliable top-13 carrier, achieved 76.7% schedule reliability in December 2025 -- meaning even the best carrier misses roughly 1 in 4 schedules.

---

## 3. Cutoff Monitoring

**Who cares:** Freight forwarders, exporters, trucking companies coordinating container delivery to port.

**What data they need:** Earliest return date (ERD), documentation cutoff, VGM (verified gross mass) deadline, vessel cutoff (port cutoff), and any changes to these dates.

**How they currently solve it:** Carriers send cutoff notifications via email, but timing and format vary wildly. Some carriers update cutoffs on their portals without email notification. Forwarders maintain internal cutoff trackers (often spreadsheets) and assign staff to monitor updates.

**What is broken:** Missing a cutoff means the cargo rolls to the next available vessel. A documentation cutoff miss might delay a shipment by 7-14 days. Cutoffs can change with short notice -- a carrier may move a vessel cutoff forward by 24 hours due to early departure, and if the forwarder does not catch the change, the container sits at the terminal. The downstream cost includes storage fees, potential demurrage, rebooking fees, and customer relationship damage.

**Value of API integration:** Automated cutoff retrieval and change detection. The system can alert operations staff when a cutoff changes, calculate remaining time windows, and flag shipments at risk of missing deadlines. DCSA-compliant carriers expose cutoff data through standardized event codes (ECP, EFC, DCO, VCO, FCO, LCO), making normalization straightforward for carriers that support the standard.

---

## 4. Demurrage and Detention Avoidance

**Who cares:** Freight forwarders, importers, BCOs (beneficial cargo owners), trucking companies.

**What data they need:** Container availability date at destination port, last free day (LFD), free time allowance, per diem/demurrage rate schedule, gate-out and empty return timestamps.

**How they currently solve it:** Operations staff track free time manually, often in spreadsheets. They call terminals to confirm availability, coordinate with truckers, and hope the container clears customs before free time expires. Charges are frequently discovered after the fact on carrier invoices.

**What is broken:** The financial exposure is severe. Demurrage charges in 2025 range from $75 to $300 per container per day, with detention/per diem charges adding $125 to $175 per container per day on top. Between 2020 and 2022, nine major carriers billed approximately $8.9 billion in demurrage and detention charges and collected roughly $6.9 billion. D&D billings peaked in Q4 2024 at 85% above Q2 2020 levels. Even with the FMC's Final Rule on Detention and Demurrage Billing Practices (effective May 2024), the charges remain substantial. The core problem is information asymmetry: the carrier knows when the container is available and when free time expires, but the forwarder or importer often finds out too late to act.

**Value of API integration:** Automated tracking of container availability, free time windows, and approaching deadlines. The system can calculate days remaining, prioritize pickups by urgency, and trigger alerts when free time is running low. For a forwarder managing 500 import containers per month, even modest improvement in pickup timing -- say, reducing average demurrage exposure by 1 day per container -- could save $37,500 to $150,000 monthly.

---

## 5. Schedule Reliability Analysis

**Who cares:** Freight forwarders making routing decisions, shippers evaluating carrier contracts, procurement teams during rate negotiations, supply chain consultants.

**What data they need:** Planned departure and arrival times, actual departure and arrival times, vessel and service identifiers, historical performance by trade lane and carrier.

**How they currently solve it:** Industry reports from Sea-Intelligence (subscription-based) provide aggregate statistics. Some large shippers maintain internal databases built from manual tracking. Most forwarders rely on anecdotal experience and carrier sales presentations.

**What is broken:** Without structured historical data, carrier selection is based on rate, relationship, and gut feel rather than measured performance. Sea-Intelligence data is valuable but aggregated -- it does not tell a specific forwarder how a carrier performs on their particular lanes. In 2025, carrier reliability ranged from 61.4% (October) to 68% (mid-year), with significant variation by carrier: Maersk led at 76.7% while others fell well below 60%. Forwarders have no practical way to build lane-specific, carrier-specific scorecards from their own operational data.

**Value of API integration:** Every tracked shipment becomes a data point. Over time, the forwarder accumulates a proprietary dataset of planned vs. actual transit times, broken down by carrier, service, port pair, and season. This enables data-driven routing decisions, evidence-based rate negotiations ("your average delay on this lane is 3.2 days, which costs us X"), and exception pattern detection.

---

## 6. Booking Status Monitoring

**Who cares:** Freight forwarders, shippers, export operations teams.

**What data they need:** Booking confirmation status, amendment history, equipment release status, and rollover notifications.

**How they currently solve it:** Booking confirmations arrive via email or EDI (IFTMBF/IFTMBC messages). Status changes -- especially rollovers -- are communicated inconsistently. Some carriers send proactive rollover notices; others require the forwarder to discover the rollover when checking the shipment status.

**What is broken:** Cargo rollovers are endemic in ocean shipping. Carriers routinely overbook vessels to maximize utilization, similar to airlines overbooking flights. When space is tight or rates are high, lower-priority bookings get bumped. A rollover can delay cargo by one to two weeks and cascade through the downstream supply chain. Forwarders often discover rollovers after the fact -- the vessel sails, the container does not, and the forwarder has to explain the delay to their customer.

**Value of API integration:** Real-time booking status tracking with rollover detection. When a booking's vessel assignment changes or the booking status shifts, the system alerts operations immediately. This enables proactive rebooking, customer notification, and supply chain adjustment before the delay compounds.

---

## 7. Document Management

**Who cares:** Freight forwarders, customs brokers, importers, banks (for letters of credit).

**What data they need:** Bill of lading draft availability, B/L release status, telex release confirmation, arrival notice, and manifest data.

**How they currently solve it:** Document exchanges happen through carrier portals (download/upload), email, and occasionally EDI. Forwarders check carrier portals to see if B/L drafts are ready, submit corrections through portal interfaces, and wait for release confirmations via email.

**What is broken:** Document delays directly delay cargo clearance. A consignee cannot clear customs without a released B/L (or equivalent). Telex release requests submitted through portals may take 24-48 hours to process, and the forwarder has no visibility into the queue. DCSA has recently published an Arrival Notice 1.0.0 Standard to address the fragmented, manual methods currently used, but adoption is still early. For letters of credit, document discrepancies discovered late can trigger demurrage while corrections are made.

**Value of API integration:** Automated document status tracking. The forwarder knows the moment a B/L draft is available, can submit corrections programmatically, and receives release confirmations in real time. This tightens the clearance timeline and reduces the risk of demurrage caused by document delays.

---

## 8. Customer Communication and White-Label Tracking

**Who cares:** Freight forwarders serving shipper customers, 3PLs, digital forwarders.

**What data they need:** The same tracking milestones, ETAs, and exception data described above -- but packaged for the forwarder's own customers under the forwarder's brand.

**How they currently solve it:** Forwarders manually relay carrier updates to customers via email. Some give customers direct access to carrier portals, which undercuts the forwarder's role. Digital-native forwarders like Flexport and Freightos have built proprietary tracking portals, but traditional forwarders lack the engineering resources to do the same.

**What is broken:** The forwarder's value proposition includes end-to-end shipment management, but when customers must check carrier portals directly, the forwarder becomes invisible. Worse, manual email updates are delayed and incomplete. Enterprise shippers increasingly demand digital tracking as a baseline: over 65% of enterprise shippers now require digital visibility tools in forwarding contracts.

**Value of API integration:** Carrier API data feeds a customer-facing tracking portal under the forwarder's brand. The forwarder controls the narrative, decides what to show and when, and adds value through commentary, exception handling, and proactive communication. This is a competitive differentiator for traditional forwarders competing against digital-native entrants.

---

## 9. Analytics and Reporting

**Who cares:** Forwarder management, shipper supply chain teams, logistics consultants, carrier account managers.

**What data they need:** Aggregated transit time data, on-time performance metrics, volume trends by carrier and lane, dwell time analysis, exception frequency, and cost correlation.

**How they currently solve it:** Manual data collection from carrier portals and TMS systems, compiled into spreadsheets. Some larger organizations use BI tools fed by EDI data, but the data is often incomplete and not normalized across carriers.

**What is broken:** Without normalized, structured data across carriers, comparative analysis is impractical. A forwarder cannot easily answer: "Which carrier delivers the best transit time on Shanghai-Long Beach?" or "What percentage of our MSC shipments experienced a transshipment delay last quarter?" The data exists in carrier systems but is not accessible in an analysis-ready format.

**Value of API integration:** Every API poll produces structured, timestamped, carrier-attributed data. Over months of operation, this becomes a rich analytical dataset. The forwarder can build dashboards, generate customer reports, run carrier scorecards, and identify optimization opportunities -- all from data they own and control, with no dependency on third-party analytics platforms.

---

## 10. Compliance and Regulatory

**Who cares:** Customs brokers, importers, forwarders handling US-bound cargo, compliance officers.

**What data they need:** Vessel schedule and departure confirmation (for ISF filing deadlines), cargo manifest data, container weight verification (VGM), and pre-arrival processing timelines.

**How they currently solve it:** ISF (Importer Security Filing) must be submitted at least 24 hours before cargo is loaded onto a US-bound vessel. Forwarders track vessel loading dates manually and coordinate with customs brokers to ensure timely filing. VGM compliance is handled through carrier portals or EDI.

**What is broken:** ISF penalties start at $5,000 per violation for late or missing filings, with escalation to $10,000+ per shipment. Beyond fines, CBP may hold cargo at port for examination at the importer's expense, disrupting schedules and damaging client relationships. The filing deadline is tied to vessel loading time at the foreign port of loading, which the forwarder may not know precisely. Vessel schedule changes can shift the filing deadline without warning. DCSA has recently published a VGM standard to digitize container weight verification, but manual processes remain the norm for most participants.

**Value of API integration:** Vessel schedule data from carrier APIs provides precise loading time estimates, enabling accurate ISF deadline calculation. Automated monitoring of schedule changes triggers re-evaluation of filing deadlines. VGM data can be retrieved and submitted programmatically. The system can flag shipments approaching filing deadlines and alert compliance teams before violations occur.

---

## Summary: Use Case Assessment Matrix

| Use Case | Primary Buyer Segment | Key Data Sources | Access Frequency | Willingness to Pay | Current Solution Maturity |
|---|---|---|---|---|---|
| Shipment tracking / visibility | Forwarders, 3PLs, shippers | Transport events, equipment events, vessel info | Hourly to daily polling | High -- core operational need | Medium -- visibility platforms exist but are expensive |
| ETA prediction and exception mgmt | Forwarders, supply chain planners | ETAs, schedule changes, delay events | Continuous / event-driven | High -- prevents downstream disruption | Low-Medium -- carrier ETAs unreliable, change detection rare |
| Cutoff monitoring | Forwarders, exporters | Cutoff dates and changes (ERD, doc, VGM, vessel) | Daily, escalating near deadlines | Medium-High -- missed cutoffs are costly | Low -- mostly manual tracking |
| Demurrage and detention avoidance | Forwarders, importers, BCOs | Container availability, free time, gate events | Daily | Very High -- direct cost avoidance ($75-300/day) | Low -- most tracking is reactive |
| Schedule reliability analysis | Forwarders, procurement teams | Planned vs. actual timestamps, historical data | Batch (weekly/monthly aggregation) | Medium -- value accrues over time | Low -- limited to industry reports |
| Booking status monitoring | Forwarders, shippers | Booking confirmations, amendments, rollovers | Event-driven | Medium-High -- rollovers cause cascading delays | Low-Medium -- inconsistent carrier notifications |
| Document management | Forwarders, customs brokers, banks | B/L status, release status, arrival notices | Event-driven | Medium -- accelerates clearance | Low -- mostly portal and email-based |
| Customer communication / white-label | Forwarders (for their customers) | All tracking milestones, ETAs, exceptions | Continuous | High -- competitive differentiation | Medium -- digital forwarders have it; traditional ones do not |
| Analytics and reporting | Forwarder management, shippers | Aggregated historical tracking data | Batch (weekly/monthly) | Medium -- requires data maturity | Low -- manual spreadsheet compilation |
| Compliance and regulatory | Customs brokers, importers | Vessel schedules, VGM, manifest data | Event-driven (deadline-linked) | Medium -- penalty avoidance | Low -- manual deadline tracking |

### Reading the Matrix

The highest-impact, highest-willingness-to-pay use cases cluster around operational needs with direct financial consequences: **shipment tracking**, **demurrage avoidance**, **ETA exception management**, and **customer-facing visibility**. These four represent the strongest initial product opportunities.

**Demurrage avoidance** stands out for its concrete, per-container ROI calculation -- a forwarder can directly quantify savings against the cost of the integration. **Customer communication** is notable as a competitive moat: forwarders who can offer branded tracking portals retain customers that would otherwise gravitate toward digital-native competitors.

**Schedule reliability analysis** and **analytics** are slower-burn value propositions that compound over time as the data asset grows. They are harder to sell upfront but create significant lock-in once established.

**Cutoff monitoring** and **booking status** are high-value but depend on carrier API coverage for specific data elements -- not all carriers expose cutoff or booking data through APIs, making these use cases carrier-dependent in the near term.

---

## Sources

- [FMC Detention and Demurrage](https://www.fmc.gov/detention-and-demurrage/) -- FMC data on D&D billing and collection ($8.9B billed, $6.9B collected, 2020-2022)
- [Sea-Intelligence: Global Schedule Reliability, December 2025](https://sea-intelligence.com/press-room/368-global-schedule-reliability-drops-to-62-8-in-december-2025) -- 62.8% reliability, Maersk at 76.7%
- [Sea-Intelligence: 2026 Starts at 62.4%](https://sea-intelligence.com/press-room/373-2026-starts-with-global-schedule-reliability-of-62-4) -- January 2026 schedule reliability
- [Sea-Intelligence: October 2025 Drop to 61.4%](https://www.sea-intelligence.com/press-room/360-global-schedule-reliability-drops-to-61-4-in-october-2025)
- [FMC Final Rule on D&D Billing Practices](https://www.fmc.gov/articles/fmc-publishes-final-rule-on-detention-and-demurrage-billing-practices/) -- May 2024 effective date
- [Demurrage and Detention in 2025](https://southernstarnavigation.com/demurrage-and-detention-2025-guide/) -- per-day charge ranges
- [Average Detention and Demurrage Fees](https://www.yardview.com/post/average-detention-demurrage-fees) -- $75-$300/day demurrage, $125-$175/day detention
- [ISF Late Filing Penalties](https://cargoez.com/blog/isf-filing-penalty) -- $5,000-$10,000 per violation
- [CBP ISF Filing Requirements](https://www.help.cbp.gov/s/article/Article-1868) -- 24-hour filing deadline
- [Digital Freight Forwarding Market Report](https://finance.yahoo.com/news/digital-freight-forwarding-strategic-industry-144100366.html) -- $33.6B (2024) to $94.8B (2030), 65% enterprise shipper digital visibility requirement
- [MSC Adopts DCSA API](https://smartmaritimenetwork.com/2025/06/30/msc-adopts-dcsa-api-for-standardised-schedule-data-exchange/) -- DCSA standard adoption
- [DCSA Arrival Notice Standard](https://container-news.com/dcsa-releases-new-digital-arrival-notice-standard/)
- [DCSA VGM Standard](https://smartmaritimenetwork.com/2025/11/18/dcsa-publishes-api-standard-for-verified-container-weights/)
- [Vizion: Rolled Cargo Management](https://www.vizionapi.com/blog/what-is-rolled-cargo-or-container-how-to-manage-by-exception) -- rollover causes and impact
- [project44 Ocean Visibility](https://www.project44.com/platform/visibility/ocean/) -- 98%+ global ocean freight coverage
- [Visibility Platform Cost Comparison](https://blogs.tradlinx.com/how-much-does-project44-fourkites-or-vizion-really-cost-what-lsps-need-to-know-before-paying-for-premium-visibility-tools/) -- premium visibility platform pricing context
- [Maersk: 2025 Freight Forwarding Trends](https://www.maersk.com/insights/integrated-logistics/2024/12/20/what-to-expect-in-2025-evolving-dynamic-trends-for-freight-forwarding)
