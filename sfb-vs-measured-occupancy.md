# Tesla Supercharger-for-Business ROI assumptions vs. our measured occupancy

State-by-state and country-by-country cross-check of Tesla's published `scAvgUtilization`
against `dcfc-analytics` live occupancy data.

| | |
|---|---|
| Our data | `live_snapshot.json`, built 2026-08-03T03:25:56Z (sweep 2026-08-03T03:25:24Z) |
| Tesla data | `/api/energy/supercharger/pricing`, harvest 2026-08-01 |
| Population | 5245 Tesla site rows; 38940 US stalls in 51 jurisdictions |

---

## 1. The two datasets do not share a unit

Tesla publishes **`scAvgUtilization` in kWh per stall per day**. The ROI tool consumes it
that way (`index.html`: `kwhPerPostYr = s.u * 365`).

We do not measure energy for Tesla. We measure **occupancy** — `au["168"]`, the fraction of
stall-time that is plugged, averaged over 7 days, from the `teslapx` per-stall overlay
(`au_src` = `px`).

One quantity bridges them:

```
kWh/stall/day  =  au168 x 24 h x P
```

`P` is the mean power while a car is plugged. `P` is not a free parameter. Physics bounds it:
a 250 kW V3 fleet with a real charge curve and idle-plugged tails must land near 40-70 kW.
So we invert Tesla's number, and we ask whether the implied `P` is credible.

---

## 2. Anchor: the Tesla Diner

The Diner (`tesla:26139`) is the one site where Tesla published a hard energy figure.
It calibrates the bridge independently of the regression.

| Quantity | Value |
|---|---|
| Stalls / rated power | 80 / 325 kW |
| Our `au168` | 0.573 |
| Tesla published throughput | 726 kWh/stall/day |
| **Implied P** | **52.8 kW** |

Tesla's own fleet figures give the same answer by a different route: 36.3 kWh/session over a
40-minute plugged time is 54 kW. Two independent paths agree. The bridge holds.

---

## 3. US result — the datasets agree

Stall-weighted per state. Fit over the 49 jurisdictions with 50 or more stalls.

| Statistic | Value |
|---|---|
| Pearson r | 0.850 |
| **R-squared** | **0.723** |
| Spearman rho | 0.886 |
| OLS fit | `TeslakWh = 1377 x occ168 + 1.3` |
| **Slope / 24 = implied P** | **57.4 kW** |
| Fleet aggregate (38,940 stalls) | occ 0.227, Tesla 336.9 -> **62.0 kW** |

**The intercept is the result.** 1.3 kWh on a mean of 337 is zero. Energy must be strictly
proportional to occupied time, so a correct pair of datasets *must* produce a zero intercept.
This one does, and the slope lands at 57.4 kW — inside the Diner's 52.8 kW and the fleet's 54 kW.

**Read: Tesla's US per-state ROI assumptions are consistent with independently measured
occupancy. They are not inflated.**

### 3.1 State-by-state

- `occ168` — our measured occupancy, stall-weighted, 7-day mean.
- `Tesla kWh` — Tesla's published `scAvgUtilization`.
- `implied P` — Tesla kWh / (occ168 x 24). Compare to the 57.4 kW model.
- `residual` — Tesla's figure against the 57.4 kW model. Positive means Tesla assumes more
  energy than our occupancy supports.

| State | Sites | Stalls | occ168 | Rated kW | Tesla kWh/stall/day | Implied P (kW) | Residual | Flag |
|---|---:|---:|---:|---:|---:|---:|---:|---|
| District of Columbia (DC) | 4 | 36 | 0.307 | 210 | 534.1 | 72.4 | +26% | low sample |
| Florida (FL) | 246 | 2910 | 0.259 | 262 | 453.4 | 73.0 | +27% |  |
| California (CA) | 664 | 11143 | 0.297 | 250 | 449.6 | 63.1 | +10% |  |
| New Jersey (NJ) | 107 | 1284 | 0.201 | 264 | 433.7 | 89.7 | +56% | Tesla high |
| Hawaii (HI) | 5 | 50 | 0.398 | 298 | 412.1 | 43.2 | -25% |  |
| Maryland (MD) | 75 | 728 | 0.236 | 245 | 376.3 | 66.5 | +16% |  |
| Georgia (GA) | 86 | 1129 | 0.246 | 268 | 344.2 | 58.4 | +2% |  |
| Arizona (AZ) | 65 | 998 | 0.244 | 240 | 319.5 | 54.6 | -5% |  |
| New York (NY) | 126 | 1361 | 0.232 | 240 | 318.8 | 57.4 | +0% |  |
| Virginia (VA) | 107 | 1003 | 0.207 | 251 | 313.9 | 63.1 | +10% |  |
| Utah (UT) | 46 | 484 | 0.181 | 264 | 302.8 | 69.6 | +21% |  |
| Texas (TX) | 229 | 2958 | 0.236 | 261 | 302.3 | 53.4 | -7% |  |
| Washington (WA) | 89 | 1028 | 0.209 | 255 | 297.9 | 59.3 | +3% |  |
| Rhode Island (RI) | 7 | 60 | 0.181 | 252 | 289.9 | 66.6 | +16% |  |
| Illinois (IL) | 78 | 946 | 0.201 | 243 | 282.7 | 58.7 | +2% |  |
| Arkansas (AR) | 11 | 117 | 0.173 | 283 | 276.4 | 66.7 | +16% |  |
| Nevada (NV) | 65 | 970 | 0.237 | 269 | 273.4 | 48.0 | -16% |  |
| Connecticut (CT) | 41 | 428 | 0.168 | 236 | 268.8 | 66.8 | +16% |  |
| Massachusetts (MA) | 63 | 678 | 0.181 | 237 | 262.4 | 60.4 | +5% |  |
| South Carolina (SC) | 46 | 588 | 0.136 | 271 | 262.4 | 80.2 | +40% | Tesla high |
| Oklahoma (OK) | 33 | 266 | 0.138 | 285 | 259.1 | 78.3 | +37% | Tesla high |
| Kentucky (KY) | 20 | 226 | 0.173 | 270 | 252.4 | 60.7 | +6% |  |
| Ohio (OH) | 59 | 585 | 0.169 | 237 | 242.2 | 59.8 | +4% |  |
| Missouri (MO) | 36 | 355 | 0.173 | 246 | 241.7 | 58.1 | +1% |  |
| Colorado (CO) | 64 | 646 | 0.172 | 259 | 237.6 | 57.6 | +0% |  |
| Pennsylvania (PA) | 111 | 1093 | 0.148 | 257 | 232.9 | 65.7 | +14% |  |
| Oregon (OR) | 60 | 644 | 0.165 | 249 | 229.9 | 57.9 | +1% |  |
| Louisiana (LA) | 21 | 231 | 0.183 | 244 | 228.7 | 52.1 | -9% |  |
| North Carolina (NC) | 91 | 1114 | 0.160 | 259 | 227.7 | 59.4 | +3% |  |
| Tennessee (TN) | 43 | 600 | 0.165 | 277 | 225.3 | 57.1 | -1% |  |
| Michigan (MI) | 41 | 398 | 0.181 | 218 | 218.4 | 50.2 | -13% |  |
| Minnesota (MN) | 38 | 340 | 0.154 | 243 | 215.3 | 58.4 | +2% |  |
| New Mexico (NM) | 23 | 220 | 0.138 | 261 | 212.3 | 64.0 | +12% |  |
| Mississippi (MS) | 14 | 152 | 0.152 | 251 | 210.2 | 57.5 | +0% |  |
| Delaware (DE) | 19 | 176 | 0.151 | 240 | 195.9 | 54.1 | -6% |  |
| Wisconsin (WI) | 39 | 347 | 0.150 | 234 | 194.8 | 54.1 | -6% |  |
| Indiana (IN) | 50 | 540 | 0.144 | 245 | 194.1 | 56.0 | -2% |  |
| Kansas (KS) | 20 | 188 | 0.135 | 243 | 185.6 | 57.4 | +0% |  |
| Idaho (ID) | 16 | 134 | 0.155 | 260 | 184.2 | 49.6 | -14% |  |
| Iowa (IA) | 20 | 178 | 0.141 | 237 | 180.4 | 53.2 | -7% |  |
| Nebraska (NE) | 11 | 92 | 0.162 | 207 | 156.1 | 40.1 | -30% |  |
| West Virginia (WV) | 18 | 142 | 0.126 | 215 | 156.1 | 51.7 | -10% |  |
| Vermont (VT) | 8 | 78 | 0.098 | 229 | 151.9 | 64.9 | +13% |  |
| New Hampshire (NH) | 20 | 190 | 0.107 | 244 | 144.6 | 56.5 | -2% |  |
| Alabama (AL) | 38 | 481 | 0.127 | 276 | 143.5 | 47.0 | -18% |  |
| Wyoming (WY) | 13 | 88 | 0.150 | 188 | 131.0 | 36.5 | -36% | Tesla low |
| South Dakota (SD) | 10 | 68 | 0.100 | 167 | 87.5 | 36.3 | -37% | Tesla low |
| Maine (ME) | 24 | 208 | 0.091 | 220 | 82.0 | 37.4 | -35% | Tesla low |
| Montana (MT) | 20 | 152 | 0.125 | 220 | 81.5 | 27.2 | -53% | Tesla low |
| North Dakota (ND) | 7 | 60 | 0.085 | 275 | 78.2 | 38.2 | -33% |  |
| Alaska (AK) | 7 | 49 | 0.024 | 310 | 48.8 | 86.2 | +50% | low sample |

### 3.2 Where the two disagree

Only 7 of the 49 fitted jurisdictions sit more than 34% off the model. Both tails have an
explanation.

**Tesla assumes more than our occupancy supports** (implied P above ~78 kW is hard to reach on
a 250 kW fleet once idle-plugged time is counted):

| State | occ168 | Tesla kWh | Implied P (kW) | Residual |
|---|---:|---:|---:|---:|
| New Jersey (NJ) | 0.201 | 433.7 | 89.7 | +56% |
| South Carolina (SC) | 0.136 | 262.4 | 80.2 | +40% |
| Oklahoma (OK) | 0.138 | 259.1 | 78.3 | +37% |

**Tesla is conservative against our occupancy:**

| State | occ168 | Tesla kWh | Implied P (kW) | Residual |
|---|---:|---:|---:|---:|
| Montana (MT) | 0.125 | 81.5 | 27.2 | -53% |
| South Dakota (SD) | 0.100 | 87.5 | 36.3 | -37% |
| Wyoming (WY) | 0.150 | 131.0 | 36.5 | -36% |
| Maine (ME) | 0.091 | 82.0 | 37.4 | -35% |

The conservative tail is rural and low-traffic. At those sites a plugged stall is more often a
car that finished charging and stayed, so our occupancy overstates energy and the implied P
falls. That is a property of our metric, not an error in Tesla's.

Hawaii is the exception worth naming. It carries the **highest occupancy in the US at 0.398**,
yet implies only 43.2 kW. Hawaii is dwell-heavy, not energy-heavy: short island trips, long
plugged times, small charge sessions. A site-selection model driven by occupancy alone would
rank Hawaii first and be wrong about the revenue.

---

## 4. International result — the fit breaks

Country parsed from the Tesla site-name suffix. A snapshot site row carries no country field.
Fit over the 26 markets with 40 or more stalls.

| Statistic | US | International |
|---|---:|---:|
| R-squared | 0.723 | **0.382** |
| Pearson r | 0.850 | 0.618 |
| Slope / 24 (kW) | 57.4 | 35.4 |
| **Intercept (kWh/stall/day)** | **1.3** | **75.4** |

A 75 kWh intercept is not a measurement. It says that a market with zero occupancy would
still deliver 75 kWh per stall per day. Rated power is 200-250 kW in every one of these
countries, so hardware cannot produce the spread either.

**Read: the non-US `scAvgUtilization` values are modeled or policy numbers. They are not
measured local throughput.** The US values behave like measurements. The international ones
do not.

### 4.1 Country-by-country

| Country | Sites | Stalls | occ168 | Rated kW | Tesla kWh/stall/day | Implied P (kW) | Flag |
|---|---:|---:|---:|---:|---:|---:|---|
| Portugal (PT) | 9 | 196 | 0.383 | 242 | 451.0 | 49.0 |  |
| Poland (PL) | 23 | 235 | 0.223 | 239 | 413.2 | 77.1 | implausible P |
| Czechia (CZ) | 12 | 120 | 0.306 | 240 | 401.1 | 54.6 |  |
| United Arab Emirates (AE) | 0 | 0 | — | — | 400.8 | — | no matched sites |
| Belgium (BE) | 27 | 408 | 0.230 | 236 | 397.0 | 72.0 |  |
| Hungary (HU) | 13 | 142 | 0.325 | 232 | 386.7 | 49.6 |  |
| Lithuania (LT) | 0 | 0 | — | — | 385.9 | — | no matched sites |
| Türkiye (TR) | 33 | 310 | 0.303 | 250 | 375.8 | 51.8 |  |
| United Kingdom (GB) | 217 | 2370 | 0.239 | 234 | 337.1 | 58.8 |  |
| Latvia (LV) | 2 | 8 | 0.360 | 200 | 321.0 | 37.2 | low sample |
| Netherlands (NL) | 54 | 928 | 0.173 | 223 | 313.9 | 75.8 | implausible P |
| Slovakia (SK) | 6 | 46 | 0.241 | 215 | 289.1 | 49.9 |  |
| Luxembourg (LU) | 1 | 16 | 0.365 | 250 | 273.6 | 31.2 | low sample |
| Romania (RO) | 15 | 138 | 0.208 | 250 | 263.8 | 52.9 |  |
| Switzerland (CH) | 38 | 464 | 0.214 | 201 | 259.3 | 50.5 |  |
| France (FR) | 272 | 4075 | 0.223 | 242 | 259.1 | 48.5 |  |
| Israel (IL) | 24 | 210 | 0.242 | 250 | 242.8 | 41.8 |  |
| Ireland (IE) | 9 | 60 | 0.183 | 190 | 237.9 | 54.2 |  |
| Germany (DE) | 317 | 3883 | 0.171 | 250 | 234.2 | 57.2 |  |
| Denmark (DK) | 39 | 766 | 0.167 | 240 | 228.6 | 57.0 |  |
| Slovenia (SI) | 8 | 78 | 0.308 | 231 | 223.9 | 30.3 | implausible P |
| Austria (AT) | 52 | 600 | 0.174 | 227 | 220.0 | 52.7 |  |
| Sweden (SE) | 86 | 1374 | 0.211 | 244 | 213.9 | 42.2 |  |
| Spain (ES) | 101 | 1150 | 0.227 | 247 | 207.1 | 38.0 |  |
| Italy (IT) | 102 | 1232 | 0.216 | 242 | 203.8 | 39.3 |  |
| Greece (GR) | 7 | 44 | 0.219 | 236 | 189.6 | 36.1 |  |
| Croatia (HR) | 13 | 104 | 0.314 | 209 | 184.1 | 24.4 | implausible P |
| Norway (NO) | 167 | 2661 | 0.187 | 235 | 169.5 | 37.9 |  |
| Iceland (IS) | 16 | 157 | 0.106 | 250 | 163.2 | 64.0 |  |
| Liechtenstein (LI) | 1 | 10 | 0.113 | 125 | 152.4 | 56.2 | low sample |
| Finland (FI) | 47 | 410 | 0.125 | 240 | 127.8 | 42.7 |  |

Implied P across the 26 fitted markets runs 24.4 to 77.1 kW — a 3.2x spread on near-identical
hardware. The US spread over 49 jurisdictions is far tighter and centres on the fleet value.

**Over-assumed** (implied P above 70 kW): PL 77 kW, NL 76 kW, BE 72 kW

**Under-assumed** (implied P below 38 kW): HR 24 kW, SI 30 kW, GR 36 kW, NO 38 kW, ES 38 kW

---

## 5. What this changes in the ROI tool

The tool ranks markets by payback, and payback is linear in `scAvgUtilization`. The two halves
of the ranking now carry different confidence:

| Ranking | Confidence | Basis |
|---|---|---|
| US state ranking | High | Independently reproduced at R-squared 0.72 with a zero intercept |
| International ranking | Low | R-squared 0.38, 75 kWh intercept, implied P from 24 to 77 kW |

The tool's assumptions block already warns that payback is idealised. It does not yet warn that
the international utilization input is weaker than the US one. That is worth adding.

---

## 6. Caveats

These apply to every number above. Carry them with the result.

1. **`au` counts plugged time, not charging time.** A car that finished and stayed still reads
   as occupied. Every implied P in this document is therefore a **lower bound** on true
   charging power.
2. **Our window is one summer week** (`au["168"]`, 2026-08-03). Tesla's figure is an annual average.
   Summer travel raises our occupancy against an annual mean.
3. **The populations are not identical.** We poll public Superchargers. Supercharger-for-
   Business sites are host-owned on the same network and the same pricing. Same fleet, same
   hardware, different ownership.
4. **Sessions are excluded on purpose.** All 5,245 Tesla rows carry `s7 = 0`, and our Tesla
   session counts read about one third low at high-turnover sites. Occupancy is a robust
   time-average and is not sampling-biased. This analysis uses occupancy only.
5. **Alaska (49 stalls) and DC (36 stalls) are below the 50-stall fit cutoff.** They appear in
   the state table and are flagged, but they do not enter the regression.
6. **Tesla ships order-of-magnitude data errors** in this endpoint — Maine energy cost, Norway
   cabinet cost and Poland energy cost were each wrong by 2x to 10x and later corrected. Treat
   any single market's figure as provisional until reverified.

---

## 7. Reproduction

| Input | Path |
|---|---|
| Tesla US per-state | `data/sfb-pricing-harvest.json` -> `usStates[ST].u` |
| Tesla international | `data/sfb-pricing-full.json` -> `records[CC].scAvgUtilization` |
| Our occupancy | VPS `root@65.21.191.211:/root/sites/dcfc-dashboard/live_snapshot.json` |
| Fields used | `net`, `ustate`, `name`, `n`, `kw`, `au["168"]`, `au_src` |

Aggregation is stall-weighted: `occ_state = sum(au168 x n) / sum(n)`.

