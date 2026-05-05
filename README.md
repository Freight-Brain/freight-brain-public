# FreightBrain

**Oilfield fleet management and demand intelligence platform — built for the field, not a boardroom.**

Live platform for NoxTerra and Lux Xpress, operating in the Powder River Basin, Uinta Basin, Greater Green River, and DJ Basin (Wyoming / Colorado / Utah).

---

## What It Is

FreightBrain is a full-stack operations platform for oilfield sand hauling. Built from the ground up by an operator, for operators — every feature exists because someone in the field needed it.

It handles everything from daily load logging and driver settlement to 6-month demand forecasting powered by live Enverus permit data.

---

## What's Live Today

### Operations Dashboard
A real-time Glass HUD dashboard used daily by dispatchers and owner-operators.

| Module | What It Does |
|---|---|
| **Fleet** | Live Samsara telemetry — fuel level, engine state, RPM, odometer, HOS per driver |
| **Loads** | Load creation, status tracking, BOL photo upload with AI field extraction |
| **Jobs** | Job management with duty status, archiving, and per-job intelligence |
| **Drivers** | Driver profiles, compliance fields, document storage |
| **Settlement** | Per-operator settlement calculation tied to actual load and diesel data |
| **Diesel Log** | Fuel card reconciliation by driver and entity |
| **Water Tickets** | Water hauling ticket tracking |
| **Maintenance** | Fleet maintenance log |
| **BOL Upload** | Tablet and mobile BOL capture with AI extraction |
| **SMS Log** | Twilio inbound/outbound message log |
| **Intel Feed** | Market signal feed — WTI, rig counts, permit activity |

### Tablet PWA
A driver-facing Progressive Web App for the field. Hook → load → unload cycle, BOL capture, HOS visibility. Runs on any Android tablet with a browser, no app store required.

### Role-Based Access
- **Admin** — full platform access (dispatcher / owner)
- **Operator** — scoped access for owner-operators running their own MC

---

## MiroFish — Demand Intelligence Engine

The flagship feature. A multi-agent forecasting system that predicts truck demand 6 months out across all active oilfield basins.

### How It Works

```
Enverus API (13,000+ permits)
        ↓
  Permit Scraper → Rig Analyzer → Market Signals (EIA/WTI)
        ↓                ↓                ↓
        └────────── Forecast Engine ──────┘
                         ↓
              26-Week Demand Curve
              Per-Zone / Per-Commodity
                         ↓
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
- **MiroFish pipeline execution** — connect the run-scraper trigger to actually fire the full agent pipeline on demand
- **Commodity alert** — expand Monday alert to include per-commodity demand breakdown and capacity utilization by equipment type
- **Completions data** — wire Enverus `/completions` endpoint when subscription tier allows; currently estimating frac activity from permits + rigs

### Medium-Term
- **Multi-tenant** — FreightBrain as a SaaS platform for other oilfield carriers. Role isolation, per-company data partitioning, white-label theming
- **Nemotron-Parse integration** — swap BOL AI extraction from Anthropic to NVIDIA NIM (Nemotron-Parse VLM) — purpose-built for document field extraction, likely cheaper and more accurate at scale
- **Natural language query** — Intelligence tab query box: "how many loads did we run on EOG Redwood last month?" → auto-generates and runs Supabase SQL
- **Auto-dispatch intelligence** — MiroFish demand signal feeds into load recommendation engine (which jobs to pursue, which operators to call, optimal equipment mix)

### Longer-Term
- **Autonomous agent layer** — MiroFish evolves from a scheduled report to a persistent multi-agent system: permit watcher, market signal monitor, dispatch coordinator, settlement reviewer — running 24/7 with human oversight and budget controls
- **Load scoring for spot market** — AI-scored load recommendations for owner-operators when direct jobs have gaps (rate-per-mile, deadhead, regional risk)
- **EvictionShield** — separate product, same stack. AI-powered tenant legal defense platform. Stage 1 live. Stage 2 (document upload + AI extraction) pending.

---

## Enverus — Why It Matters Here

MiroFish is built on Enverus DirectAccess as its primary intelligence layer. The permit → rig → forecast pipeline depends on the quality and recency of that data.

Current integration:
- Pulling permits across 14 states via `/v3/permits` with county-level filtering
- Active rig counts via `/v3/rigs` with `ActiveStatus` filtering
- Operator-level aggregation for the weekly call list
- Basin-level geographic mapping (40+ named basins across the US)

The `completions` endpoint (frac activity) is the next unlock — it would replace our estimated frac signal with actual completion data, materially improving weeks 4-12 forecast accuracy.

We're also interested in what Enverus has on the production data side for modeling the produced water haul demand tail (Phase 3 of a well's lifecycle is currently estimated from permit depth + well type alone).

---

## Organization

**GitHub:** [Freight-Brain](https://github.com/Freight-Brain)

Active repositories:
- `freight-brain` — main platform (this repo)
- `eviction-shield` — tenant legal defense platform
- `clawdia-memory` — AI assistant persistent memory

---

## Contact

**Victor Caballero**
Owner — NoxTerra / Lux Xpress
[LinkedIn] | vocaballero67@gmail.com

Built with [Clawdia](https://clawdia.noxterra.co) — an AI executive embedded in operations.
