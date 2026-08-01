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

All data pulled from Tesla's `/api/energy/supercharger/pricing` endpoint via the [Supercharger For Business configurator](https://www.tesla.com/supercharger-for-business/get/overview). **Last refreshed 2026-08-01.** Raw archives live in `data/`.

The endpoint is Akamai-gated — `curl` and other automation get a 403. Pull it with a same-origin `fetch` from a logged-in browser tab.

### API semantics
`stateCode` is required and validated **for the US only**; a bogus US state returns HTTP 500. Outside the US the parameter is ignored entirely — bogus, empty, and omitted all return the same national record. So an HTTP 500 always means "market not configured", never a wrong region code. That makes a country sweep reliable: hit every ISO 3166-1 alpha-2 code with no `stateCode` and read 200 vs 500.

### Coverage (2026-08-01)
- **32 markets priced.** Swept all 249 ISO alpha-2 codes: 31 non-US returned pricing, 217 returned 500.
- **28 publicly launched** (localized configurator page returns 200). Up from 11 on 2026-06-04.
- **4 priced with no public page:** `AE` `LV` `TR` `LI`.
- **Announced but unpriced:** Saudi Arabia, Qatar, Jordan — all still 500.
- Liechtenstein (`LI`, ISO 438) is absent from world-atlas 110m, so it is counted in the data and ranked in the table but cannot render on the world map. Showing it would require the 50m atlas (739K vs 105K).

## Disclaimer

Outputs are for indicative purposes only and should not be considered official predictions from Tesla. Tesla makes no guarantee of profitability. Actual results can differ materially from these calculations.

## Author

**Branden Flasch** - [bflasch.com](https://bflasch.com) | [YouTube](https://youtube.com/@brandenflasch) | [X](https://x.com/brandenflasch) | [LinkedIn](https://www.linkedin.com/in/bflasch/) | [GitHub](https://github.com/brandenflasch)
