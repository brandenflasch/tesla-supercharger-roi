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
- **Maine ($0.008/kWh)** is flagged as a likely data anomaly (actual commercial rates are ~$0.15-0.18/kWh). A one-click fix to override Maine's energy cost to $0.15/kWh is included.
- Assumes ~15% energy loss from grid to vehicle battery (baked into Tesla's energy cost figures)
- Excludes parking rent and hardware taxes

## Tech Stack

Single self-contained HTML file. No build step.

- **D3.js v7** + **TopoJSON** - US state map rendering
- **Chart.js v4** - 15-year projection chart
- **Google Fonts** (Sora + IBM Plex Mono)
- Dark theme with `prefers-color-scheme` light mode support

## Data Source

All data pulled from Tesla's `/api/energy/supercharger/pricing` endpoint (April 2026) via the [Supercharger For Business configurator](https://www.tesla.com/supercharger-for-business/get/overview). The API returns per-state values for `averageEnergyCost`, `scMedianPriceToEVDriver`, and `scAvgUtilization`, keyed by `stateCode`.

## Disclaimer

Outputs are for indicative purposes only and should not be considered official predictions from Tesla. Tesla makes no guarantee of profitability. Actual results can differ materially from these calculations.

## Author

**Branden Flasch** - [bflasch.com](https://bflasch.com) | [YouTube](https://youtube.com/@brandenflasch) | [X](https://x.com/brandenflasch) | [LinkedIn](https://www.linkedin.com/in/bflasch/) | [GitHub](https://github.com/brandenflasch)
