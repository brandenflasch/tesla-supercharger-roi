# Tesla SfB ROI assumptions vs. measured occupancy — November refresh

State-by-state and country-by-country cross-check of Tesla's published `scAvgUtilization`
against `dcfc-analytics` live occupancy. This run replaces the August 2026 run.

| | |
|---|---|
| Our data | `live_snapshot.json`, built 2026-11-03T04:23:01Z (sweep 2026-11-03T04:22:09Z) |
| Tesla data | `/api/energy/supercharger/pricing`, re-harvested 2026-11-03 |
| Population | 5,479 Tesla site rows; 38,517 US stalls in 51 jurisdictions; 23,121 international stalls in 31 priced markets |

---

## 0. What changed since August — read this first

**1. Tesla's numbers did not move at all.** A full 248-code ISO sweep plus all 51 US
jurisdictions returned byte-identical values to the 2026-08-01 harvest, across all 13
pricing fields. Same 31 international markets, same 51 states. Nothing drifted.

**2. Our occupancy fell almost everywhere.** US fleet `au168` went 0.227 → 0.195, a 14%
drop. Rural and cold states fell hardest: MT −61%, WY −51%, SD −45%, ME −42%. Tourist
markets abroad fell further: HR −71%, SI −47%, NO −46%, IT −44%, SE −42%, PT −36%.

**3. That reverses the August international conclusion.** The August run read
R-squared 0.38 internationally and called the non-US values "modeled or policy numbers".
Most of that failure was **summer tourism**, not bad data. The same test now reads
**R-squared 0.81–0.84**. The international fit is close to the US fit.

**4. AE and LT now have matched sites.** In August we had zero Supercharger sites in either.
We now have AE 28 sites / 219 stalls and LT 1 site / 8 stalls. The "cannot be checked"
caveat is retired for AE.

**5. `au["720"]` is not a 30-day window.** It is an exact copy of `au["168"]` on all 5,225
rows that carry both. Do not use it. `au["1440"]` is genuine (60 days, Sep 4 – Nov 3) and
this run uses it as the primary window.

---

## 1. The bridge

Tesla publishes **`scAvgUtilization` in kWh per stall per day**. We measure **occupancy** —
the fraction of stall-time that is plugged, from the `teslapx` per-stall overlay
(`au_src` = `px`, 5,460 of 5,479 Tesla rows).

```
kWh/stall/day  =  occupancy x 24 h x P
```

`P` is the mean power while a car is plugged. We invert Tesla's number and ask whether the
implied `P` is credible for a 250 kW fleet with a real charge curve and idle-plugged tails.

---

## 2. The seasonality problem — and why one week is not enough

Tesla's figure is an **annual average**. Our `au168` is **one week**. The August run sampled
a summer-peak week; this run samples a November week. The implied `P` moves with the season
even though Tesla's number is fixed:

| Window | US implied P | International implied P |
|---|---:|---:|
| Aug 3, 7-day (summer peak) | 57.4 kW | 35.4 kW |
| Nov 3, 7-day (low season) | 75.4 kW | 48.0 kW |
| **Nov 3, 60-day (Sep 4 – Nov 3)** | **70.8 kW** | **50.6 kW** |

Both regions scale by about the same factor from August to November (US ×1.31,
international ×1.36). That is the signature of a seasonal shift in the denominator, not a
change in Tesla's data. The 7-day and 60-day columns cover the **same 38,517 US and 23,010
international stalls**, so the difference between them is the window, not the site mix.

**Read: a single-week occupancy window cannot calibrate the absolute kW. Use the 60-day
window, and treat any implied-P figure as the season it was measured in.**

### The Tesla Diner anchor moved too

| Run | Diner occupancy | Tesla published | Implied P |
|---|---:|---:|---:|
| Aug 3 (`au168`) | 0.573 | 726 kWh/stall/day | 52.8 kW |
| Nov 3 (`au168`) | 0.514 | 726 kWh/stall/day | 58.8 kW |
| Nov 3 (`au1440`, 60 d) | 0.515 | 726 kWh/stall/day | 58.7 kW |

Tesla's own fleet figure — 36.3 kWh/session over a 40-minute plugged time — is 54 kW.
The Diner now reads 58.7 kW. Both sit inside the plausible 45–70 kW band.

---

## 3. Results

| Statistic | US Aug 3 | US Nov 3 (60 d) | Intl Aug 3 | Intl Nov 3 (60 d) |
|---|---:|---:|---:|---:|
| Jurisdictions fitted | 49 | 50 | 26 | 27 |
| Pearson r | 0.850 | 0.924 | 0.618 | 0.896 |
| **R-squared** | 0.723 | **0.864** | 0.382 | **0.812** |
| Spearman rho | 0.886 | 0.918 | — | 0.886 |
| Slope / 24 = implied P | 57.4 kW | 70.8 kW | 35.4 kW | 50.6 kW |
| **Intercept (kWh/stall/day)** | **+1.3** | **-36.0** | **+75.4** | **+60.4** |
| Intercept as % of mean | +0.4% | -11% | +30% | +24% |
| Fleet aggregate implied P | 62.0 kW | 64.0 kW | — | 66.5 kW |

**The US relationship is strong and linear in both runs.** R-squared rose from 0.72 to
0.86. The intercept is -36.0 kWh on a mean of
336, or -11% of the mean — still small,
but no longer the near-exact zero the August run produced.

**The international relationship is now almost as strong.** R-squared
0.81 against the US 0.86. The implied-P spread across
fitted markets narrowed from 3.2x in August to 2.0x now.

**What survives from August:** the international intercept is still positive and still
larger than the US one — +60 kWh (+24% of
the mean) against -36 (-11%). A positive
intercept is not physical. So the international input is still the weaker of the two — but
the evidence is **much weaker than the August run claimed**, and the "modeled or policy
number" reading is no longer supported.

### 3.1 US — all 51 jurisdictions

Fit over the 50 jurisdictions with 50 or more stalls. Residual is against the
70.8 kW 60-day model.

| Market | Sites | Stalls | occ 7d | occ 60d | Tesla kWh/stall/day | Implied P 7d (kW) | Implied P 60d (kW) | Residual 60d | Flag |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---|
| District of Columbia (DC) | 4 | 36 | 0.305 | 0.332 | 534.1 | 73.0 | 67.0 | +1% | low sample |
| Florida (FL) | 247 | 2925 | 0.230 | 0.253 | 453.4 | 82.1 | 74.6 | +15% |  |
| California (CA) | 655 | 10890 | 0.254 | 0.288 | 449.6 | 73.6 | 64.9 | -1% |  |
| New Jersey (NJ) | 103 | 1224 | 0.177 | 0.190 | 433.7 | 102.3 | 95.2 | +51% | Tesla high |
| Hawaii (HI) | 6 | 62 | 0.300 | 0.314 | 412.1 | 57.2 | 54.6 | -17% |  |
| Maryland (MD) | 72 | 714 | 0.226 | 0.233 | 376.3 | 69.4 | 67.3 | +4% |  |
| Georgia (GA) | 86 | 1147 | 0.219 | 0.234 | 344.2 | 65.4 | 61.2 | -5% |  |
| Arizona (AZ) | 65 | 994 | 0.170 | 0.213 | 319.5 | 78.4 | 62.6 | -2% |  |
| New York (NY) | 110 | 1185 | 0.207 | 0.214 | 318.8 | 64.1 | 62.0 | -3% |  |
| Virginia (VA) | 106 | 1003 | 0.191 | 0.209 | 313.9 | 68.5 | 62.6 | -2% |  |
| Utah (UT) | 47 | 511 | 0.158 | 0.182 | 302.8 | 80.0 | 69.4 | +11% |  |
| Texas (TX) | 238 | 3048 | 0.201 | 0.221 | 302.3 | 62.8 | 56.9 | -11% |  |
| Washington (WA) | 91 | 1039 | 0.207 | 0.211 | 297.9 | 59.9 | 58.9 | -8% |  |
| Rhode Island (RI) | 7 | 60 | 0.157 | 0.169 | 289.9 | 76.9 | 71.6 | +16% |  |
| Illinois (IL) | 76 | 944 | 0.181 | 0.199 | 282.7 | 65.1 | 59.2 | -6% |  |
| Arkansas (AR) | 11 | 117 | 0.140 | 0.172 | 276.4 | 82.1 | 66.8 | +8% |  |
| Nevada (NV) | 65 | 973 | 0.165 | 0.196 | 273.4 | 69.0 | 58.1 | -8% |  |
| Connecticut (CT) | 39 | 412 | 0.166 | 0.172 | 268.8 | 67.6 | 65.2 | +5% |  |
| Massachusetts (MA) | 59 | 634 | 0.183 | 0.194 | 262.4 | 59.6 | 56.3 | -11% |  |
| South Carolina (SC) | 45 | 584 | 0.117 | 0.143 | 262.4 | 93.8 | 76.6 | +27% | Tesla high |
| Oklahoma (OK) | 38 | 286 | 0.099 | 0.119 | 259.1 | 108.7 | 90.3 | +55% | Tesla high |
| Kentucky (KY) | 19 | 218 | 0.156 | 0.182 | 252.4 | 67.4 | 57.6 | -8% |  |
| Ohio (OH) | 58 | 573 | 0.152 | 0.176 | 242.2 | 66.2 | 57.3 | -8% |  |
| Missouri (MO) | 37 | 363 | 0.146 | 0.170 | 241.7 | 69.1 | 59.3 | -4% |  |
| Colorado (CO) | 63 | 639 | 0.145 | 0.169 | 237.6 | 68.1 | 58.6 | -5% |  |
| Pennsylvania (PA) | 108 | 1056 | 0.137 | 0.152 | 232.9 | 71.1 | 63.7 | +4% |  |
| Oregon (OR) | 62 | 668 | 0.140 | 0.150 | 229.9 | 68.5 | 63.8 | +5% |  |
| Louisiana (LA) | 22 | 239 | 0.146 | 0.178 | 228.7 | 65.4 | 53.4 | -15% |  |
| North Carolina (NC) | 90 | 1086 | 0.150 | 0.171 | 227.7 | 63.1 | 55.5 | -11% |  |
| Tennessee (TN) | 45 | 612 | 0.145 | 0.174 | 225.3 | 64.9 | 54.0 | -13% |  |
| Michigan (MI) | 39 | 382 | 0.142 | 0.172 | 218.4 | 64.2 | 52.8 | -15% |  |
| Minnesota (MN) | 38 | 342 | 0.133 | 0.151 | 215.3 | 67.3 | 59.4 | -2% |  |
| New Mexico (NM) | 23 | 220 | 0.105 | 0.137 | 212.3 | 84.1 | 64.5 | +8% |  |
| Mississippi (MS) | 14 | 160 | 0.116 | 0.137 | 210.2 | 75.8 | 63.9 | +7% |  |
| Delaware (DE) | 18 | 164 | 0.131 | 0.152 | 195.9 | 62.2 | 53.7 | -12% |  |
| Wisconsin (WI) | 39 | 353 | 0.118 | 0.143 | 194.8 | 68.8 | 56.7 | -6% |  |
| Indiana (IN) | 51 | 543 | 0.118 | 0.146 | 194.1 | 68.7 | 55.4 | -9% |  |
| Kansas (KS) | 19 | 184 | 0.116 | 0.142 | 185.6 | 66.7 | 54.4 | -10% |  |
| Idaho (ID) | 18 | 150 | 0.108 | 0.131 | 184.2 | 71.0 | 58.5 | -2% |  |
| Iowa (IA) | 20 | 178 | 0.114 | 0.138 | 180.4 | 65.7 | 54.5 | -9% |  |
| Nebraska (NE) | 11 | 92 | 0.101 | 0.126 | 156.1 | 64.2 | 51.8 | -12% |  |
| West Virginia (WV) | 18 | 142 | 0.100 | 0.123 | 156.1 | 65.3 | 52.7 | -10% |  |
| Vermont (VT) | 8 | 78 | 0.072 | 0.116 | 151.9 | 87.4 | 54.6 | -6% |  |
| New Hampshire (NH) | 19 | 180 | 0.090 | 0.113 | 144.6 | 67.2 | 53.2 | -8% |  |
| Alabama (AL) | 36 | 457 | 0.105 | 0.130 | 143.5 | 57.2 | 46.1 | -22% |  |
| Wyoming (WY) | 15 | 104 | 0.073 | 0.102 | 131.0 | 74.3 | 53.6 | -4% |  |
| South Dakota (SD) | 10 | 68 | 0.056 | 0.081 | 87.5 | 65.7 | 45.0 | -14% |  |
| Maine (ME) | 24 | 208 | 0.053 | 0.076 | 82.0 | 64.7 | 45.2 | -11% |  |
| Montana (MT) | 21 | 160 | 0.049 | 0.079 | 81.5 | 69.9 | 42.8 | -18% |  |
| North Dakota (ND) | 7 | 60 | 0.058 | 0.073 | 78.2 | 56.3 | 44.3 | -12% |  |
| Alaska (AK) | 7 | 50 | 0.031 | 0.036 | 48.8 | 65.2 | 55.9 | +89% | Tesla high |

### 3.2 International — 31 priced markets

Fit over the 27 markets with 40 or more stalls.

| Market | Sites | Stalls | occ 7d | occ 60d | Tesla kWh/stall/day | Implied P 7d (kW) | Implied P 60d (kW) | Residual 60d | Flag |
|---|---:|---:|---:|---:|---:|---:|---:|---:|---|
| Portugal (PT) | 9 | 204 | 0.243 | 0.285 | 451.0 | 77.2 | 66.0 | +11% |  |
| Poland (PL) | 27 | 287 | 0.196 | 0.187 | 413.2 | 87.9 | 92.2 | +44% | Tesla high |
| Czechia (CZ) | 12 | 120 | 0.317 | 0.283 | 401.1 | 52.8 | 59.1 | -1% |  |
| United Arab Emirates (AE) | 27 | 215 | 0.274 | 0.311 | 400.8 | 60.9 | 53.7 | -8% |  |
| Belgium (BE) | 27 | 416 | 0.256 | 0.244 | 397.0 | 64.7 | 67.7 | +11% |  |
| Hungary (HU) | 14 | 158 | 0.262 | 0.239 | 386.7 | 61.5 | 67.4 | +10% |  |
| Lithuania (LT) | 1 | 8 | 0.142 | 0.101 | 385.9 | 113.2 | 159.2 | +111% | low sample |
| Türkiye (TR) | 35 | 338 | 0.216 | 0.233 | 375.8 | 72.5 | 67.3 | +10% |  |
| United Kingdom (GB) | 227 | 2448 | 0.258 | 0.245 | 337.1 | 54.5 | 57.2 | -6% |  |
| Latvia (LV) | 2 | 8 | 0.416 | 0.398 | 321.0 | 32.1 | 33.6 | -41% | low sample |
| Netherlands (NL) | 54 | 940 | 0.223 | 0.203 | 313.9 | 58.7 | 64.3 | +2% |  |
| Slovakia (SK) | 6 | 46 | 0.281 | 0.228 | 289.1 | 42.9 | 52.7 | -14% |  |
| Luxembourg (LU) | 1 | 16 | 0.327 | 0.333 | 273.6 | 34.9 | 34.2 | -41% | low sample |
| Romania (RO) | 16 | 146 | 0.177 | 0.167 | 263.8 | 62.0 | 66.0 | +0% |  |
| Switzerland (CH) | 39 | 472 | 0.197 | 0.187 | 259.3 | 54.9 | 57.8 | -10% |  |
| France (FR) | 279 | 4140 | 0.155 | 0.142 | 259.1 | 69.4 | 76.2 | +12% |  |
| Israel (IL) | 25 | 226 | 0.188 | 0.201 | 242.8 | 53.8 | 50.4 | -20% |  |
| Ireland (IE) | 14 | 104 | 0.131 | 0.126 | 237.9 | 75.4 | 78.7 | +12% |  |
| Germany (DE) | 326 | 3955 | 0.141 | 0.136 | 234.2 | 69.1 | 71.7 | +4% |  |
| Denmark (DK) | 40 | 780 | 0.130 | 0.131 | 228.6 | 73.2 | 73.0 | +4% |  |
| Slovenia (SI) | 8 | 78 | 0.164 | 0.171 | 223.9 | 56.9 | 54.6 | -16% |  |
| Austria (AT) | 54 | 606 | 0.150 | 0.141 | 220.0 | 61.2 | 65.2 | -5% |  |
| Sweden (SE) | 92 | 1432 | 0.124 | 0.114 | 213.9 | 72.1 | 78.4 | +8% |  |
| Spain (ES) | 104 | 1160 | 0.150 | 0.151 | 207.1 | 57.6 | 57.0 | -15% |  |
| Italy (IT) | 106 | 1250 | 0.122 | 0.129 | 203.8 | 69.6 | 65.7 | -6% |  |
| Greece (GR) | 8 | 44 | 0.144 | 0.147 | 189.6 | 55.0 | 53.6 | -21% |  |
| Croatia (HR) | 17 | 132 | 0.090 | 0.109 | 184.1 | 84.8 | 70.2 | -5% |  |
| Norway (NO) | 171 | 2688 | 0.102 | 0.114 | 169.5 | 69.5 | 62.0 | -15% |  |
| Iceland (IS) | 17 | 161 | 0.066 | 0.076 | 163.2 | 103.3 | 89.8 | +7% |  |
| Liechtenstein (LI) | 1 | 10 | 0.121 | 0.109 | 152.4 | 52.5 | 58.3 | -21% | low sample |
| Finland (FI) | 48 | 422 | 0.090 | 0.100 | 127.8 | 59.0 | 53.3 | -30% | Tesla low |

---

## 4. Markets we now see but Tesla does not price

Our snapshot carries matched Supercharger sites in seven markets with no SfB pricing record:
Mexico (184 stalls), Saudi Arabia (48), Estonia (23), Bulgaria (18), Jordan (14),
Qatar (12), Serbia (12). Canada is priced, but only with a province code —
`countryCode=CA` alone returns HTTP 400.

---

## 5. What this changes in the ROI tool

| Ranking | Confidence | Basis |
|---|---|---|
| US state ranking | High | R-squared 0.86, intercept -11% of the mean |
| International ranking | Moderate | R-squared 0.81, intercept +24% of the mean |

The August note shipped into the tool said the international input fails at R-squared 0.38
with a 24–77 kW spread. **That note overstated the case and has been corrected.**

---

## 6. Caveats

1. **Occupancy counts plugged time, not charging time.** Every implied P here is a
   **lower bound** on true charging power.
2. **Even 60 days is not a year.** The window is Sep 4 – Nov 3. Tesla's figure is annual.
   Compare implied-P levels only against other runs of the same window length.
3. **`au["720"]` is a duplicate of `au["168"]`.** Fixed-window analysis must use `au["1440"]`
   or `au["168"]`, never `au["720"]`.
4. **The populations are not identical.** We poll public Superchargers; SfB sites are
   host-owned on the same network and pricing.
5. **Sessions are excluded on purpose.** This analysis uses occupancy only.
6. **Tesla ships order-of-magnitude data errors** in this endpoint. Treat any single
   market's figure as provisional until reverified.

---

## 7. Reproduction

| Input | Path |
|---|---|
| Tesla US per-state | `data/sfb-pricing-harvest.json` -> `usStates[ST].u` |
| Tesla international | `data/sfb-pricing-full.json` -> `records[CC].scAvgUtilization` |
| Our occupancy | VPS `root@65.21.191.211:/root/sites/dcfc-dashboard/live_snapshot.json` |
| Fields used | `net`, `ustate`, `name`, `n`, `kw`, `au["168"]`, `au["1440"]`, `au_src` |
| Aggregation script | VPS `/root/attic/occ_agg1440.py` |

Aggregation is stall-weighted: `occ = sum(au x n) / sum(n)`. Country is parsed from the
country name in the `name` suffix; a snapshot site row carries no country field.
Canadian provinces and Puerto Rico are excluded from both panels.
