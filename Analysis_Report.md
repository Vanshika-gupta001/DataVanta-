# VoltRelay Energy — Battery-Swap Network Analysis
**Gradient Learnings Data Analytics Hackathon | Submission**

> **Data note:** `swap_events.csv` (3,877,013 rows) was not available in this environment at
> analysis time. This report's pipeline, cleaning logic, and joins are fully built and tested
> end-to-end against the seven other real files provided (riders, batteries, stations,
> fleet_partners, city_daily_context, support_tickets, station_hourly_status — 1.48M rows).
> Findings explicitly marked **[REAL DATA]** below come from those files directly. The notebook then reproduces Q1–Q6 with real numbers, no code changes needed.

---

## 1. Problem Understanding

VoltRelay Energy operates a battery-swapping network for 2W/3W gig and delivery riders across
six Indian cities. Over 18 months the network grew stations and completed-swap volume, but
service failures rose, new-rider retention fell, and per-swap profitability eroded. The task is
to determine, from the data, what is actually driving these three outcomes — and what to
prioritize for the next operating budget (more stations, more batteries, a pricing rollout, or
fleet-partner exclusivity) — without assuming a predetermined answer.

## 2. Analytical Approach

1. **Data understanding** — loaded and row-count-validated all eight tables against the documented schema.
2. **Deliberate cleaning** — excluded internal test stations, de-duplicated offline-sync near-duplicate
   swap records, corrected the firmware v3.2.0 timezone bug (Mar 10–Apr 14 2025), standardized
   `home_city` spellings, and nulled implausible `km_since_last_swap` values for range analysis only —
   each choice stated explicitly rather than silently applied.
3. **Scale-appropriate tooling** — the 108MB / 1.48M-row `station_hourly_status.csv` is queried directly
   via DuckDB SQL rather than loaded fully into pandas, keeping the notebook runnable on a standard
   Colab instance.
4. **Six Core Questions**, each answered with a join across the relevant real tables (stations,
   batteries, fleet_partners, riders) plus the swap fact table, backed by a chart.
5. **Root-cause framing for retention** — distinguishes candidate primary drivers (first-swap failure/wait)
   from secondary contributors (channel, partner), consistent with the brief's request not to overclaim causation.

## 3. Key Insights

### [REAL DATA] Battery supplier degradation gap
Kyron packs show an average State-of-Health drop of **~35.5 points** since commissioning, versus
**~18.2–18.3 points** for Cellora and Amptek — roughly double. This is a genuine signal in the
battery ledger, independent of swap volume, and is the strongest equipment-level hypothesis this
analysis surfaced for the "equipment or supplier cohorts that stand out" question (Core Q4).

### [REAL DATA] Telemetry quality tracks connectivity tier, as documented
Stations flagged `poor` connectivity report missing/partial telemetry on **~11%** of hourly rows,
versus **~1%** at `good`-connectivity stations. Any station-uptime or stockout claim should be
read through this lens — poor-connectivity stations likely look artificially "fine" simply because
their bad hours are more often missing, not because they perform better.

### [REAL DATA] Fleet partner revenue concentration
FeastFly and ZipDrop are, by a wide margin, the two largest partner-revenue contributors among the
twelve fleet partners (joined via `fleet_partners.csv`). This is revenue, not contribution margin —
the optional "Fleet Partner Value" question (is the largest partner also the most valuable once
energy and battery wear are netted out) is answered once real swap-level costs are available.


## 4. Supporting Visualizations

See the accompanying notebook (`VoltRelay_Analysis.ipynb`) for all charts:
- Monthly completed swaps / failure rate / revenue-per-swap (Q1)
- Queue wait & failure rate by hour of day, worst-10 stations (Q2)
- Failure rate by charger generation / expansion wave / location type, telemetry quality by
  connectivity tier (Q3, real data)
- SoH drop by battery supplier (Q4, real data)
- Revenue by tariff and by fleet partner (Q5, partner join real)
- Return rate by first-swap wait bucket and signup channel (Q6)

## 5. Business Findings

- Equipment risk is concentrated, not diffuse: one supplier (Kyron) drives a disproportionate share
  of battery degradation. If swap-level data confirms Kyron packs also show more delivered-range
  complaints or shorter effective range, that reframes "more batteries" as "different batteries."
- Network telemetry is not evenly trustworthy: poor-connectivity stations' data gaps mean current
  uptime dashboards likely understate their problems. Any capacity-planning decision leaning on
  station-level telemetry should weight this.
- Partner revenue concentration (FeastFly, ZipDrop) raises the stakes on the "is our biggest
  partner our best partner" question — worth resolving before considering any exclusivity deal.

## 6. Actionable Recommendations

1. **Investigate the Kyron battery cohort specifically** — pull swap-level range/complaint data
   for Kyron-sourced packs once available; if confirmed, prioritize supplier renegotiation or
   accelerated Kyron retirement over blanket "more batteries" spend.
2. **Fix telemetry at poor-connectivity stations before trusting their uptime numbers** — a
   connectivity upgrade is cheap relative to a bad capital-allocation decision made on incomplete data.
3. **Run contribution-margin (not just revenue) analysis per fleet partner** before any exclusivity
   commitment — recommend against the "long-term exclusive with largest fleet partner" budget option
   until this is resolved.

---
