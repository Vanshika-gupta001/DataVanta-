# VoltRelay — Battery Lifecycle & Supplier Quality Analytics

Hackathon submission analyzing EV battery-swap fleet operations (batteries,
stations, support tickets, city context, fleet partners).

**Data gap, disclosed upfront:** `swap_events.csv` (rider-level swap
transactions) was referenced in the brief but never made available. This
project is scoped to what the real, uploaded data actually supports —
battery/supplier quality, station operational reliability, and support-ticket
patterns — rather than filling the gap with invented numbers.

## Key Finding

Kyron-supplied batteries degrade roughly 2x faster than the other two
suppliers and retire at an **88% rate vs 0%** for Amptek/Cellora
(Welch's t-test, p < 0.001; Cohen's d ≈ 0.78). Manufacturing-lot, firmware,
pack-type, and fleet-age were checked as possible confounds and ruled out —
the effect holds across all of them. Flagged as a procurement risk, not
yet a proven root cause (see report for what would confirm it).

## Files

| File | What it is |
|---|---|
| [`voltrelay_analysis.ipynb`](./voltrelay_analysis.ipynb) | Full notebook — data quality checks, EDA, all core questions, runs top-to-bottom |
| [`REPORT.md`](./REPORT.md) | Detailed write-up: methodology, findings, statistical evidence, recommendations |
| [`charts/`](./charts) | Generated visualizations (fleet composition, degradation rates, confound checks) |
| [`tables/`](./tables) | Summary CSVs backing the charts and report tables |
| [`linkedin_post.md`](./linkedin_post.md) | Post used for the hackathon's personal-branding streak |

## How to Run

```bash
pip install pandas numpy scipy duckdb matplotlib
jupyter nbconvert --to notebook --execute voltrelay_analysis.ipynb
```

## Scope Notes

- Two files present in the raw upload were excluded as unrelated to this
  dataset: `business_sales_sample.csv` and `tasks.r`.
- `STN-TST-01` / `STN-TST-02` (internal test rigs) are excluded from all
  station-level aggregates.
- Full data-quality decisions and their reasoning are documented in the
  notebook's first section, not repeated per analysis.
