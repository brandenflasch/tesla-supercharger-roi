# Tesla Supercharger for Business: State-by-State ROI Analysis

Interactive visualization showing the profitability of hosting Tesla Superchargers across all 50 US states + DC, based on data from [Tesla's Supercharger For Business page](https://www.tesla.com/supercharger-for-business/get/overview).

**Live:** [brandenflasch.github.io/tesla-supercharger-roi](https://brandenflasch.github.io/tesla-supercharger-roi/)

## What It Does

Takes Tesla's per-state electricity costs, EV driver selling prices, and utilization data and calculates ROI metrics for every jurisdiction. The visualization lets you explore which states are most (and least) profitable for Supercharger site hosts.

### Key Metrics Per State
- **Gross Margin** = Selling Price - Energy Cost - Tesla PPU Fee ($0.10/kWh)
- **Annual Profit Per Post** = Gross Margin x Utilization x 365
- **Payback Period** = Total Cost Per Post / Annual Profit Per Post
- **Site Investment** = Cost Per Post x Post Count
- **15-Year Projection** = Year 1 profit compounded at 7% YoY utilization growth

### Flat Costs (Same Nationally)
| Component | Cost |
|-----------|------|
| Cabinet | $55,000/post |
| Other Hardware | $3,000/post |
| Services | $3,000/post |
| Shipping | $1,500/post |
| Installation (Low/Med/High) | $45K / $55K / $65K per post |
| Tesla PPU Fee | $0.10/kWh (ongoing) |

### Variable Data (Per State)
- Average Energy Cost ($/kWh)
- Median Price to EV Driver ($/kWh)
- Average Utilization (kWh/post/day)

## Features

### Interactive US Map
- D3.js + TopoJSON with accurate Albers projection (AK/HI insets)
- Color-coded by 6 selectable metrics: Payback Period, Annual Profit, Gross Margin, Utilization, Utility Rate, Sell Rate
- Hover tooltips with full state breakdown
- State abbreviation labels

### Controls
- **Color By** dropdown (6 metrics)
- **Installation Cost** selector (Low $45K / Medium $55K / High $65K / Custom)
- **Post Count** selector (4 / 8 / 16 / 24 / Custom)
- Cabinet count auto-calculated (posts / 8)

### Rankings Sidebar
- Top 10 and Bottom 10 states with color-coded background bars
- Dynamically re-sorts and re-titles based on active metric
- Hover to highlight corresponding state on map

### KPI Summary Bar
- Median payback period
- Best and worst state
- Number of states under 5-year payback

### 15-Year Projection Chart
- Chart.js line chart showing cumulative profit from Year 0 through Year 15
- Includes 7% YoY utilization growth compounding
- Green above break-even, red below
- State selector dropdown

### Sortable Data Table
- All 51 jurisdictions with every metric
- Click column headers to sort ascending/descending
- Color dots matching the map for each state

### Data Notes
- Assumes ~15% energy loss from grid to vehicle battery (baked into Tesla's energy cost figures)
- Excludes parking rent and hardware taxes

### Known Tesla data errors (all since corrected upstream)
Tesla has shipped several order-of-magnitude errors in this API. Each was caught by sanity-checking a value against real commercial rates, and each was later fixed by Tesla:

| Market | Field | Bad value | Corrected | Caught |
|---|---|---|---|---|
| Maine | energy cost | $0.008/kWh | $0.293/kWh | Apr 2026 |
| Norway | cabinet | 39,300 NOK (~10× low) | 357,600 NOK | Jun 2026 |
| Poland | energy cost | 0.45 PLN/kWh (~2× low) | 0.95 PLN/kWh | Aug 2026 |

The lesson: treat any energy cost far below local commercial rates as suspect until reverified.

## Tech Stack

Single self-contained HTML file. No build step.

- **D3.js v7** + **TopoJSON** - US state map rendering
- **Chart.js v4** - 15-year projection chart
- **Google Fonts** (Sora + IBM Plex Mono)
- Dark theme with `prefers-color-scheme` light mode support

## Data Source

All data pulled from Tesla's `/api/energy/supercharger/pricing` endpoint via the [Supercharger For Business configurator](https://www.tesla.com/supercharger-for-business/get/overview). **Last refreshed 2026-10-22.** All 31 earlier international markets and all 51 US jurisdictions are unchanged since 2026-08-01. New: Canada (per-province pricing). Raw archives live in `data/`.

The endpoint is Akamai-gated — `curl` and other automation get a 403. Pull it with a same-origin `fetch` from a logged-in browser tab.

### API semantics
`stateCode` is required and validated **for the US and Canada only**; a bogus US or Canadian state returns HTTP 500, and Canada with no `stateCode` returns HTTP 400 (`Missing required fields`). Everywhere else the parameter is ignored entirely — bogus, empty, and omitted all return the same national record. So an HTTP 500 always means "market not configured", never a wrong region code. That makes a country sweep reliable: hit every ISO 3166-1 alpha-2 code with no `stateCode` and read 200 vs 500. Treat a 400 as "priced per region" and re-query with region codes (this is how Canada appeared on 2026-10-22).

### Coverage (2026-10-22)
- **33 markets priced.** Swept all 249 ISO alpha-2 codes: 31 non-US returned pricing, 216 returned 500, `CA` returned 400 (needs a province).
- **29 publicly launched** (localized `/supercharger-for-business/get/overview` returns 200). Up from 28 on 2026-10-01 (Canada: `en_CA`, `fr_CA`).
- **4 priced with no public configurator:** `AE` `LV` `TR` `LI`. `AE` and `TR` now serve a localized `/supercharger-for-business` info page, but it has no configurator link and `/get/overview` still 404s.
- **Announced but unpriced:** Saudi Arabia, Qatar, Jordan — pricing still 500. Each now has an info page with no configurator, like `AE` and `TR`.

### Canada (new 2026-10-22, CAD)
Per-province, like the US. The page shows Ontario (largest market), the same way the US row uses California. NT, NU and YT return 500.

Shared by every province: Tesla fee 0.14/kWh (PPU) and 0.085 (non-PPU), cabinet 78,000, other hardware 4,200, services 4,200, shipping 2,500, install 65,000 / 78,000 / 90,000.

| Province | Energy $/kWh | Sell $/kWh | Util kWh/post/day |
|---|---|---|---|
| AB | 0.293 | 0.908 | 121.5 |
| BC | 0.092 | 0.908 | 248.3 |
| MB | 0.274 | 0.908 | 70.8 |
| NB | 0.273 | 0.908 | 87.5 |
| NL | 0.127 | 0.908 | 202.9 |
| NS | 0.184 | 0.908 | 71.2 |
| ON | 0.16 | 0.908 | 231.2 |
| PE | 0.337 | 0.908 | 79.9 |
| QC | 0.044 | 0.908 | 211.5 |
| SK | 0.574 | 0.902 | 52.9 |

Quebec's 0.044 is the lowest energy cost in the whole dataset. Per the data-error lesson above, verify it against Hydro-Québec's published commercial rate before relying on it.
- Liechtenstein (`LI`, ISO 438) is absent from world-atlas 110m, so it is counted in the data and ranked in the table but cannot render on the world map. Showing it would require the 50m atlas (739K vs 105K).

## Semi Charging for Business

A separate section covers Tesla's Semi charger program (Megacharger + Basecharger).

- **Markets (verified 2026-09-14):** US, DE, FR, NL, BE (`nl_BE` + `fr_BE`), GB. Each localized `/semi-charging-for-business` page returns 200. `en_CA`, `fr_CH`, `de_AT`, `it_IT` and `es_ES` return 404.
- **Endpoint:** `/api/energy/semi-charging/pricing?countryCode={CC}&productType=SemiCharger&stateCode=CA`. The configurator hardcodes `stateCode=CA`. Same Akamai gate as the Supercharger endpoint: the Puppeteer browser gets 403, so pull it from a logged-in Chrome tab.
- **Fields:** `semiChargerMegachargerCostPerPost`, `semiChargerBasechargerCostPerPost`, `semiChargerServicesCostPerPost`, `semiChargerShippingCostPerPost`, `semiChargerAllInclusiveFee`, `currencyCode`.
- **No payback.** The Semi record has no energy cost, driver price, utilization or install tiers, so the section shows purchase price only.
- **Est. purchase price** uses the configurator formula: `megaCost × ceil(megaPosts/2) × 2 + baseCost × basePosts + (services + shipping) × allPosts`. Taxes and installation are excluded.
- Data lives in the `SEMI` object in `index.html`. Set `SEMI_PRICED_ASOF` after a pull. Raw archive: `data/semi-pricing-2026-09-15.json`.

### Semi pricing (2026-09-15)
| Market | Ccy | Megacharger/post | Basecharger/post | Services/post | Shipping/post | Fee/kWh | Discounted fee |
|---|---|---|---|---|---|---|---|
| US | USD | 89,500 | 15,500 | 3,000 | 1,500 | 0.08 | 0.03 |
| DE / FR / NL / BE | EUR | 77,675 | 13,175 | 2,550 | 1,275 | 0.08 | 0.03 |
| GB | GBP | 66,625 | 11,625 | 2,250 | 1,125 | 0.08 | 0.03 |

- US 2-post Megacharger = 89,500×2 + 4,500×2 = **$188,000**, which matches Electrek's May 1 report.
- Basecharger, services and shipping are exactly 0.85× US (EUR) and 0.75× US (GBP). Megacharger is not: EUR is 0.8679× and GBP is 0.7444×.
- The fee is 0.08 in every currency (not FX-scaled). `semiChargerAllInclusiveFeeDiscounted` (0.03) is not read by Tesla's configurator.
- Akamai can answer HTTP 429 with `{"cpr_chlge":"true"}` after heavy automated traffic. A normal page load in Chrome cleared it.

## Disclaimer

Outputs are for indicative purposes only and should not be considered official predictions from Tesla. Tesla makes no guarantee of profitability. Actual results can differ materially from these calculations.

## Author

**Branden Flasch** - [bflasch.com](https://bflasch.com) | [YouTube](https://youtube.com/@brandenflasch) | [X](https://x.com/brandenflasch) | [LinkedIn](https://www.linkedin.com/in/bflasch/) | [GitHub](https://github.com/brandenflasch)
