# FreightBrain

**Oilfield fleet management and demand intelligence platform — built for the field, not a boardroom.**

Live platform for NoxTerra and Lux Xpress, operating in the Powder River Basin, Uinta Basin, Greater Green River, and DJ Basin (Wyoming / Colorado / Utah).

---

## What It Is

FreightBrain is a full-stack operations platform for oilfield sand hauling. Built from the ground up by an operator, for operators — every feature exists because someone in the field needed it.

It handles everything from daily load logging and driver settlement to 6-month demand forecasting powered by live Enverus permit data. The long-term vision is a single platform where one owner-operator can run a 25-truck fleet with no back-office employees — because the AI handles everything a dispatcher, settlement clerk, and compliance manager would do.

---

## What's Live Today

### Operations Dashboard
A real-time Glass HUD dashboard used daily by dispatchers and owner-operators.

| Module | What It Does |
|---|---|
| **Fleet** | Live Samsara telemetry — fuel level, engine state, RPM, odometer, HOS per driver |
| **Loads** | Load creation, status tracking, BOL photo upload with AI field extraction |
| **Jobs** | Job management with duty status, archiving, per-job intelligence |
| **Drivers** | Driver profiles, compliance fields, document storage |
| **Settlement** | Per-operator settlement calculation tied to actual load and diesel data |
| **Diesel Log** | Fuel card reconciliation by driver and entity |
| **Water Tickets** | Water hauling ticket tracking |
| **Maintenance** | Fleet maintenance log |
| **BOL Upload** | Tablet and mobile BOL capture with AI field extraction |
| **SMS Log** | Twilio inbound/outbound message log |
| **Intel Feed** | Market signal feed — WTI, rig counts, permit activity |
| **HOS Dashboard** | Hours of Service compliance view per driver, color-coded drive/shift/cycle remaining |
| **DVIR** | Driver Vehicle Inspection Reports — defect photos, safe/unsafe/resolved status per truck |

### Tablet PWA
A driver-facing Progressive Web App for the field. Hook → load → unload cycle, BOL capture, HOS visibility. Runs on any Android tablet with a browser, no app store required.

### Employment Packets
Digital onboarding for drivers — captured, stored, and reviewed directly in the platform. Applications tab for admin review and approval workflow.

### Role-Based Access
- **Admin** — full platform access (dispatcher / owner)
- **Operator** — scoped access for owner-operators running their own MC

---

## MiroFish — Demand Intelligence Engine

The flagship feature. A multi-agent forecasting system that predicts truck demand 6 months out across all active oilfield basins.

### How It Works

```
Enverus API (13,000+ permits)
        |
  Permit Scraper → Rig Analyzer → Market Signals (EIA/WTI)
        |                |                |
        └────────── Forecast Engine ──────┘
                         |
              26-Week Demand Curve
              Per-Zone / Per-Commodity
                         |
            Projection Tab  +  Monday Alert
```

### Data Sources
- **Enverus DirectAccess** — live drilling permits, active rig counts, operator activity (14 states)
- **EIA API** — WTI crude, diesel, natural gas prices
- **WOGCC / FracFocus / PipelineWY** — Wyoming-specific permit and completion data
- **NOAA** — weather signal layer
- **USDA Crop Progress** — seasonal demand context

### What It Produces

A territory-scoped 26-week demand forecast broken down by:

- **Zone** — Powder River Basin, Uinta Basin, Greater Green River, DJ Basin (Colorado), plus national basin visibility (Permian, Bakken, Eagle Ford, Marcellus, etc.)
- **Commodity** — sand, gravel, fresh water, produced water, drill cuttings, mud
- **Fleet sizing** — trucks needed at current capacity vs. growth target, by equipment type (hoppers, tankers, side dumps, belly dumps)
- **Operator signals** — top operators by permit velocity, 30-day window (who to call this week)
- **Market sentiment** — BULLISH / NEUTRAL / BEARISH based on WTI + permit trend

### Output
- Live **Projection tab** in the dashboard with chart, basin browser, zone breakdown, commodity split, capacity utilization meters
- **Monday 6am MT alert** delivered to operations team with weekly summary + fleet sizing recommendation

### Current Numbers (May 2026)
- 13,425 total permits pulled from Enverus (2,803 after 120-day recency filter)
- PRB: 522 permits, 13 active rigs
- Uinta: 190 permits, 10 active rigs
- GGR: 66 permits, 3 active rigs
- DJ: 13 permits
- WTI: $95.43 at last run — sentiment BULLISH
- Top operators by permit velocity: Anschutz (160), Occidental (122), EOG (108)

---

## Safety & Compliance Architecture

This is where FreightBrain goes beyond a dispatch tool. The platform is being built toward continuous, AI-driven compliance — not paperwork that happens after something goes wrong.

### Driver Compliance Passport

A portable, driver-owned credential record that travels with the driver between carriers. Rather than rebuilding a qualification file from scratch every time a driver changes fleets, the Passport holds:

- CDL class and endorsements (hazmat, tanker, doubles/triples)
- Medical certification and expiration
- H2S and safety training records
- Equipment certifications (which truck types, which commodities)
- FMCSA Clearinghouse status
- MVR history
- Prior haul history and performance data

Cryptographically verifiable — a carrier can confirm a driver's credentials without needing to call the prior carrier. A driver can take their history with them. The long-term standard we are working toward is compatible with FMCSA's existing identity infrastructure and decentralized identifier (DID) frameworks.

### Load Matching by Driver Capability

Loads are matched against the Passport automatically. The system knows before it asks:

- Is this driver certified for this commodity? (H2S, hazmat, tanker endorsement)
- Is this driver legal to run this many miles on their current HOS?
- Does their equipment match the job? (hopper vs. tanker vs. side dump)
- Do they have the required site safety training for this operator?

This eliminates the dispatcher bottleneck on routine assignments and prevents non-compliant assignments from happening in the first place.

### Anti-Double-Brokering

A persistent load fingerprint system tracks every load from creation through delivery. Each load gets a unique identifier tied to the originating job, confirmed by BOL at pickup and delivery. The system flags any attempt to re-tender a load that is already in-transit or to submit duplicate BOLs against the same ticket number. At scale, this becomes a network-level protection — loads that have been touched by the platform carry a verifiable chain of custody.

### Facial Recognition — Time and Attendance

Truck-level time card verification using facial recognition at clock-in. The driver who logged the hours is the driver who started the truck. Prevents:

- Ghost employees collecting time they didn't work
- Drivers logging hours for someone else
- Time card fraud on owner-operator settlements

Implementation is camera-based at the yard or tablet-mounted in the cab — no dedicated hardware required beyond what already exists for BOL capture.

### Anti-Theft

Geofencing combined with ignition and telemetry monitoring. If a truck leaves a defined zone without an active load assigned to a verified driver, the system fires an alert immediately. Ties into the Samsara integration already live in the Fleet tab. Future state: remote immobilization integration for recovery scenarios.

### Continuous HOS Compliance Prediction

The system doesn't just show current HOS status — it predicts violations before they happen. Given a driver's current cycle, their assigned load distance, estimated completion time, and any mandatory rest breaks required, it surfaces a warning at the point where dispatch would need to act, not after the driver has already gone illegal.

### DVIR Integration

Driver Vehicle Inspection Reports already live in the platform — per-truck inspection cards, defect photos, safe/unsafe/resolved status pulled from Samsara. The next step is AI triage: flagging defects that constitute out-of-service conditions before a driver pulls onto the highway.

---

## The 25-Truck Vision

The platform is being built around a specific hypothesis: one owner-operator should be able to run 25 trucks with no back-office employees.

Today that's impossible because the coordination layer — dispatch, settlement, compliance, driver qualification, load assignment — requires human staff. FreightBrain replaces that layer with AI:

| Function | Today | FreightBrain |
|---|---|---|
| Dispatch | Human dispatcher | MiroFish demand signal + auto load matching |
| Settlement | Manual spreadsheet | Auto-generated from loads + diesel log |
| Compliance | Manual DQ file tracking | Continuous Passport monitoring |
| Load assignment | Phone calls | Capability-matched, HOS-verified auto-assignment |
| Driver onboarding | Paper packets | Digital employment packet + Passport issuance |
| Maintenance | Gut feel | DVIR-triggered, mileage-interval alerts |
| Market intelligence | Word of mouth | MiroFish 6-month demand forecast |

The owner's job becomes: own the trucks, maintain the relationships, make the strategic calls. Everything else runs.

---

## Tech Stack

| Layer | Technology |
|---|---|
| Backend | Node.js / Express (ESM) |
| Database | Supabase (Postgres) |
| Hosting | Fly.io (2 machines, auto-start/stop) |
| File Storage | Cloudflare R2 |
| Telematics | Samsara API |
| SMS | Twilio |
| AI / Document Parse | Anthropic Claude (BOL extraction, intelligence) |
| Forecasting Data | Enverus DirectAccess API |
| Energy Data | EIA Open Data API |
| Frontend | Vanilla JS / HTML (Glass HUD — dark, orange accents, cyan, JetBrains Mono) |
| Driver App | PWA (no app store) |

---

## Roadmap

### Near-Term
- **Auto-settlement** — weekly settlement packets generated automatically from loads + diesel log, delivered to each operator
- **MiroFish pipeline execution** — connect the run-scraper trigger to fire the full agent pipeline on demand
- **Commodity alert** — expand Monday alert to include per-commodity demand breakdown and capacity utilization by equipment type
- **Completions data** — wire Enverus `/completions` endpoint when subscription tier allows; currently estimating frac activity from permits + rigs

### Medium-Term
- **Driver Compliance Passport v1** — portable credential record, carrier-verifiable, driver-owned
- **Capability-based load matching** — loads matched to drivers by Passport, HOS, and equipment automatically
- **Facial recognition time cards** — camera-based clock-in verification at yard and cab
- **Anti-double-brokering fingerprinting** — load chain-of-custody from tender to delivery
- **Multi-tenant** — FreightBrain as a SaaS platform for other oilfield carriers. Role isolation, per-company data partitioning, white-label theming
- **Natural language query** — Intelligence tab query box: "how many loads did we run on EOG Redwood last month?" → auto-generates SQL and returns results
- **Nemotron-Parse integration** — swap BOL AI extraction from Anthropic to NVIDIA NIM, purpose-built for document field extraction

### Longer-Term
- **25-truck autonomous operations** — one owner-operator, no back-office employees, full AI coordination layer
- **Transparent pricing layer** — fair-market rate ranges generated from observed data, eliminating broker information asymmetry
- **SBIR Phase I** — formal R&D track under DOT/FMCSA: AI orchestration for small-carrier compliance, safety, and transparent market reasoning. Noxterra as the live testbed.
- **Autonomous agent layer** — MiroFish evolves to a persistent multi-agent system: permit watcher, market signal monitor, dispatch coordinator, settlement reviewer — 24/7 with human oversight and budget controls
- **Cross-border capability** — Mexico freight corridor

---

## Enverus — Why It Matters Here

MiroFish is built on Enverus DirectAccess as its primary intelligence layer. The permit → rig → forecast pipeline depends on the quality and recency of that data.

Current integration:
- Pulling permits across 14 states via `/v3/permits` with county-level filtering
- Active rig counts via `/v3/rigs` with `ActiveStatus` filtering
- Operator-level aggregation for the weekly call list
- Basin-level geographic mapping (40+ named basins across the US)

The `completions` endpoint (frac activity) is the next unlock — replacing our estimated frac signal with actual completion data would materially improve weeks 4-12 forecast accuracy.

We're also interested in production data for modeling the produced water haul demand tail. Phase 3 of a well's lifecycle (produced water) is currently estimated from permit depth and well type alone — actual production volumes would sharpen that signal significantly.

---

## Organization

**GitHub:** [Freight-Brain](https://github.com/Freight-Brain)

Active repositories:
- `freight-brain` — main platform
- `eviction-shield` — AI-powered tenant legal defense platform
- `clawdia-memory` — AI assistant persistent memory

---

## Contact

**Victor Caballero**
Owner — NoxTerra / Lux Xpress
Douglas, Wyoming
admin@noxterra.co | 970-413-9775

**Clawdia** — AI Executive
clawdia@noxterra.co | [clawdia.noxterra.co](https://clawdia.noxterra.co)

Built with [Clawdia](https://clawdia.noxterra.co) — an AI executive embedded in operations.
