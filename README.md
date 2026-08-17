[README_1.md](https://github.com/user-attachments/files/31157003/README_1.md)
# AI Data Center Unit Economics — Five-Country Model

A bottoms-up unit-economics model of a 100 MW AI data center, built to answer one question: **how does national energy security affect the viability of a data center project?**

The model runs a single-hold liquidation DCF (build, operate 5 years, liquidate) across five countries, each representing a distinct energy security archetype: Norway (concentrated hydro), Saudi Arabia (abundant hydrocarbon), India (cheap power, high cost of capital), the United States (resilient grid, long queues), and Ireland (moratorium-constrained supply).

## Key Findings

**Base case NPV (contracted, $m):** Norway 1,448 > Saudi Arabia 1,429 > India 12 > United States −282 > Ireland −461.

There is no unconditional winner. Economics, energy security, and carbon emissions each indicate a different leader:

- **Time-to-power is the binding constraint.** Each year in the interconnection queue accrues holding cost and exposes the project to compute-price erosion. Countries with long grid queues (US 4.5 years, Ireland 6 years) enter the market at a collapsed GPU rental price and go negative even with contracted offtake.
- **Offtake contracts are necessary but not sufficient.** Contracts lift NPV by raising occupancy, lowering WACC, and locking the entry price against erosion. But they cannot rescue a site that waits too long for power.
- **Energy cost is immaterial at today's prices.** Power is ~3% of revenue for fast-queue countries. It crosses to material only where a long queue erodes compute price to the floor *and* local power is expensive (Ireland).
- **The trade-offs are real.** Saudi Arabia is strong on economics and security but produces the highest carbon emissions. Norway wins on economics and carbon but concentrates 88% of generation in hydro, exposing drought risk. The US has the most energy-secure grid but long queues push base-scenario NPV negative.

The NPV ranking is invariant to compute-price scenario — the ordering holds across bear, base, and bull.

## Methodology

The model is a per-period unlevered discounted cash flow. Revenue is GPU compute rental ($/GPU-hr × density × hours × occupancy); costs are power, O&M, insurance, and property tax. Capital expenditure is phased facility construction plus GPU procurement at operations start. Terminal value is facility shell salvage plus residual GPU value.

**Compute-price erosion:** the model applies a calendar-coupled price path. Entry price = P0 × (1 − decline)^TTP, where P0 is today's rental rate, decline is a scenario-linked annual rate (base −20%), and TTP is time-to-power. Contracted deals lock the entry price; merchant deals continue eroding to a floor ($2.50, the estimated full break-even for a low-cost producer). This means a slow build enters an already-eroded market — the queue costs you the market, not just time.

**Architecture:** the model follows River/FAST financial modeling standards — one-directional flow, blue inputs / black calcs / red outputs, sheet protection with only assumption cells unlocked, and structural divider tabs.

## Model Structure (33 tabs)

**Inputs (10 tabs)**

| Tab | Purpose |
|---|---|
| Cover | Thesis, scope disclosures, format key |
| Model map | Tab-by-tab flow diagram |
| Limitations | Scope boundaries, conditional assumptions, and caveats |
| Sources | 25+ sourced rows with named source, vintage, confidence rating, and clickable URL |
| GPU Specs | NVIDIA GB200 NVL72 datasheet — 1200W, 192GB HBM3e, 600/MW density derivation |
| Compute Price Data | Azure H100 SXM retail prices (120 data points, 24 regions × 5 tiers) + getDeploying neocloud snapshot |
| Interconnection Queue | LBNL US queue data (1,297 operational projects) — live median/percentile for US TTP |
| Power Build-up | IEA/IEX wholesale prices + grid connection + firming costs per country |
| WACC Build-up | USD risk-free (FRED DGS10) + Damodaran country risk premiums + disclosed equity premium |
| Assumptions | All model inputs — 17 key drivers with bear/base/bull bands, country data, deal structure |

**Calculations (6 tabs)**

| Tab | Purpose |
|---|---|
| Capex Stack | Facility + GPU + interconnection capital cost build-up |
| Unit Economics | Per-MW roll-up with external anchor checks ($30–50m capex/MW; $15–40m revenue/MW/yr) |
| Buildout Schedule | Phased facility capex draw schedule (even draw over construction period) |
| Calculations | Single-country per-period cash flow (revenue → EBITDA → tax → operating CF → capex → net FCF → discounted → cumulative) |
| Country Engine | All 5 countries × 2 deal types computed in parallel per-period blocks, plus instant-power counterfactuals and sensitivity overrides |
| Price Floor | Full-cost break-even derivation per country — the floor = low-cost producer (Norway/Saudi ~$2.50/GPU-hr) |

**Outputs (13 tabs)**

| Tab | Purpose |
|---|---|
| Comparison | Five-country NPV, IRR, payback, break-even utilization side-by-side |
| Sensitivity | Two-way price × utilization grid + one-way tornado (ranked by NPV swing) |
| Price Path | Compute-price erosion trajectories per country |
| Country detail | Single-country net FCF + cumulative cash flow chart |
| Power Timing | Instant-power counterfactual — what NPV would be if grid connection were immediate |
| Deal Comparison | Contracted vs. merchant evaluated in parallel, independent of toggle |
| Asset Life | Queue + hold + shell-residual timeline per country |
| Energy Security | Energy security scorecard per country |
| Three-Lens View | Economics, energy security, and carbon — three lenses on the same five countries |
| Power Cost | Delivered power cost composition per country |
| Revenue Mix | Power / opex / EBITDA composition (100% stacked) |

**Checks (2 tabs)**

| Tab | Purpose |
|---|---|
| Checks | Automated reconciliation — error total must be 0; sense checks flagged for review |
| Review | Formatting and structure review log |

## Key Assumptions

| # | Driver | Bear | Base | Bull | Source |
|---|---|---|---|---|---|
| 1 | Facility capex | $24m/MW | $17m/MW | $13m/MW | Epoch AI / ModulEdge |
| 2 | GPU + IT capex | $50,000/GPU | $42,000/GPU | $35,000/GPU | SemiAnalysis GB200 NVL72 rack-allocated |
| 3 | GPU density | 600/MW | 600/MW | 600/MW | NVIDIA GB200 NVL72 datasheet |
| 4 | Compute price (P0) | $4.00/GPU-hr | $5.70/GPU-hr | $9.00/GPU-hr | getDeploying B200 + Azure H100 |
| 5 | GPU economic life | 3 yr | 5 yr | 6 yr | Hyperscaler 10-K filings |
| 6 | Paid occupancy | 88% | 88% | 88% | CBRE DC vacancy report |
| 7 | Price decline | −30%/yr | −20%/yr | −15%/yr | Scenario-linked |
| 8 | Price floor | $2.50 | $2.50 | $2.50 | Low-cost producer break-even (Norway/Saudi) |

Country-varying inputs (delivered power $/MWh, time-to-power, WACC, corporate tax, PUE, interconnection cost) are sourced individually — see the Sources tab for full citations.

## Sensitivity Analysis

One-way tornado (base/contracted/Norway, ranked by NPV swing in $m):

| Driver | Low NPV | High NPV | Swing |
|---|---|---|---|
| Compute price (P0) | 78 | 4,108 | 4,030 |
| Price decline rate | 371 | 2,040 | 1,669 |
| GPU density | 959 | 1,448 | 489 |

Compute price dominates. The price-decline lever (how fast the market erodes) is the #2 mover — a structural finding from the erosion mechanic. Energy cost ranks near the bottom, confirming the thesis that power is a time problem, not a cost problem.

## Limitations

- **Single-hold liquidation.** The model runs one GPU cycle then sells the shell. A multi-cycle owner would re-tenant, moderating the negative NPVs for slow-queue countries. The results are a conservative floor, not a full asset-life valuation.
- **Country-invariant compute price.** Revenue is applied identically across countries — no data-residency, sovereignty, or latency-driven price premia. Defensible (compute is globally traded) but stated explicitly.
- **USD-nominal, no FX.** All figures are in USD with no local-currency inflation or hedging cost modeled.
- **Unconditional chip access.** The model assumes GPU availability. Trade restrictions (export controls, tariffs) are bracketed, not priced.
- **Simplified tax.** Statutory corporate rate, unlevered, with NOL carryforward but no jurisdiction-specific incentives.
- **No water valuation.** Water availability is a live constraint in Saudi Arabia and rising in India but is not costed.

## How to Use

1. **Open in Microsoft Excel** (not Google Sheets — the model uses Data Tables and structured references that require Excel's calculation engine).
2. The workbook opens on the **Comparison** tab — the five-country dashboard.
3. Toggle **Scenario** (Bear/Base/Bull), **Country** (for drill-down), and **Deal type** (Contracted/Merchant) on the **Assumptions** tab (cells B4, B5, B7). Only blue-font cells are unlocked and editable.
4. Explore the **Sensitivity** tab for the tornado chart and price × utilization grid.
5. Check the **Checks** tab — cell C4 should read 0 (all reconciliation checks pass).

## Data Sources

All assumptions are sourced. The Sources tab lists 25+ inputs with named source, publication date, confidence rating, and a clickable URL. Primary data includes:

- NVIDIA GB200 NVL72 datasheet (GPU specs and density)
- SemiAnalysis GB200 component model (GPU + IT capex)
- getDeploying B200 snapshot, Aug 2026 (neocloud compute pricing — 110 listings, 26 providers)
- Azure Retail Prices API (hyperscaler compute pricing — 120 data points)
- LBNL Queued Up dataset (US interconnection queue — 1,297 operational projects)
- Statnett, EirGrid, CTUIL grid-operator registers (Norway, Ireland, India queue data)
- FRED DGS10 (US 10-year Treasury, 1,249 daily observations)
- Damodaran country risk premiums, Jan 2026
- IEA Electricity Mid-Year Update 2025 (wholesale power prices)
- CBRE Global Data Center Trends 2026 (vacancy and pre-lease rates)

## Built With

- FAST financial modeling standards
- PwC Global Financial Modelling Guidelines
- Excel with sheet protection, defined names, and structured references
- Python (openpyxl) for the build engine — the workbook is programmatically generated to ensure structural integrity

## Author

**Henry Dalby** — [LinkedIn](https://www.linkedin.com/in/henrydalby/)

## License

This project is licensed under the MIT License — see [LICENSE](LICENSE) for details.
