# Surf Data

This folder holds CSV files pulled from Surf. We upload each CSV here as we pull it.

DOM ids below were resolved directly against `report/crypto on the clock.html` (live at
https://ally-pantera.github.io/crypto-on-the-clock/). Where an exhibit lives inside a
multi-slide carousel, the id is the carousel `<figure>` and the specific slide is named.
Where a figure has no stable id, the enclosing section anchor is given and marked "no id".

## `surf data/` — data file → chart mapping (`crypto on the clock`)

### A. Weekly venue-mix charts — tidy-long primaries (basis: jan_jun)
| File | Report exhibit | What it provides | Window/basis |
|---|---|---|---|
| `category_weekly_long_jan_jun_chart.csv` | "Two Venues, Two Different Mixes" (`#cat-volume-chart`) | Stacked weekly category volume, absolute USD; `venue,week,category,volume_usd`; 8 categories | Kalshi 26wk (Dec29→Jun22), PM 25wk (Jan5→Jun22) |
| `duration_weekly_long_jan_jun_chart.csv` | Duration-mix weekly stacked bars, short-term section — "By duration" slide (`#crypto-breakdown-carousel`, slide 1) | `venue,week,duration_bucket,volume_usd`; backs 94% Kalshi / 79% PM short-term shares | 25wk, Jan5→Jun22 |
| `asset_weekly_long_jan_jun_chart.csv` | Asset-band weekly stacked bars — "By asset" slide (`#crypto-breakdown-carousel`, slide 2) | `venue,week,asset,volume_usd`; BTC/ETH/SOL/XRP/Other; totals K $4.48B / PM $5.59B | 25wk, Jan5→Jun22 |

Per-venue wide siblings (`*_kalshi_*`, `*_polymarket_*`) + `*_verification.json` accompany each and are 0-diff supersets/audit artifacts — supporting data, not separately rendered.

### B. Fee-by-duration charts (basis: jan_jun)
| File | Report exhibit | What it provides | Window/basis |
|---|---|---|---|
| `fees_weekly_polymarket_duration_jan_jun_chart.csv` | Polymarket fee-by-duration stacked weekly, Fees section — "Polymarket by duration (observed)" slide (`#fee-duration-carousel`, slide 1) | Buckets `5m/15m/1h-4h/daily/weekly_and_longer/total`; observed per-fill; total $53.9M | 25wk, Jan5→Jun22 |
| `fees_weekly_kalshi_duration_jan_jun_chart.csv` | Kalshi fee-by-duration stacked weekly, Fees section — "Kalshi by duration (modeled)" slide (`#fee-duration-carousel`, slide 2) | Buckets `15m_under/one_hour/intraday_daily/multiday_weekly/monthly_plus/total`; modeled; total $122.1M | 25wk, Jan5→Jun22 |

`fees_weekly_long_jan_jun_chart.csv` is the tidy-long superset of both (0-diff); supporting.

### C. Trade-size distribution (basis: jan_may — NOT June, by design)
| File | Report exhibit | What it provides | Window/basis |
|---|---|---|---|
| `trade_size_distribution_long_jan_may_chart.csv` | "Trade-size distribution by venue", Microstructure (no figure id — violin `<svg>` under section `#head-to-head`) | Violin/box + percentile dots; 16 percentile + 62 histogram rows/venue; PM median $3.05 / Kalshi $4.70; cost-basis asymmetry disclosed | Jan 1–May 28 |

Wide siblings `*_percentiles_*` / `*_histogram_*` + verification JSON are supporting. `jan_may` window is correct — this exhibit was never extended to June.

### D. Bot behavior — 5-minute Polymarket takers (basis: feb_may)
| File | Report exhibit | What it provides | Window/basis |
|---|---|---|---|
| `bot_behavior_5m_taker_long_feb_may_chart.csv` | Superset feeding all 4 bot exhibits (tidy-long master; source table, no standalone chart) | 46 rows = union of the 4 wide files; 213,111 wallets / 299M trades / $2.07B | Feb 12–May 28 |
| `bot_classification_5m_taker_shares_feb_may_chart.csv` | Trader classification (Bot-like/Sophisticated/Retail) (no figure id — figure under section `#diligence-automation`) | Bot-like 25.7% wallets / 87.9% trades / 86.3% volume | Feb 12–May 28 |
| `bot_ticket_size_5m_taker_feb_may_chart.csv` | Ticket-size distribution ¹ — "Ticket size" slide (`#tells-carousel`, slide 1) | Ticket-size shares by wallet/trade/volume | Feb 12–May 28 |
| `bot_cadence_5m_taker_feb_may_chart.csv` | Trade cadence (inter-trade gap) — "Cadence" slide (`#tells-carousel`, slide 2) | "1s or less" = 84.0% of volume | Feb 12–May 28 |
| `bot_concentration_5m_taker_feb_may_chart.csv` | Wallet concentration (top-N share) — "Concentration" slide (`#tells-carousel`, slide 3) | Top-1K 43.3% / Top-10K 73.1% of volume | Feb 12–May 28 |

### E. `settlement_study/` — settlement-manipulation exhibits (basis: May 2026)
All settlement exhibits render inside the report section anchored `#diligence-settlement`; the individual figures within have no stable id. Files live under `surf data/settlement_study/` (paths below are relative to that subfolder).
| File | Report exhibit | What it provides | Window/basis |
|---|---|---|---|
| `q3_profiles_within_cycle.json` | Within-cycle volume/return profiles (12–17× spike exhibit) — `#diligence-settlement`, no figure id | Per-second atm/decided profiles; 5m still-even vol peak/median = 17.15× | May 2026 |
| `q3_pricecurve_5m_15m.json` | Jump-and-revert price curve — `#diligence-settlement`, no figure id | bps deviation −60→+30s; peak +1.59bps @~0–3s post-close | May 2026 |
| `binance_1s/*.npy` | Raw source for profiles (no standalone chart) | 1-sec BTC OHLCV-style arrays, 2.68M rows, gap-free | May 1–Jun 1 |
| `implied_prob/*.json` | Backs still-even share figures — `#diligence-settlement`, supporting | 5m/15m/4h implied-up + PM vol; still-even 2.9% (5m) / 0.9% (15m) | May 2026 |
| `klines_walkaway/*.json` | Walk-away return exhibit (5 horizons) — `#diligence-settlement`, no figure id | OHLCV candles 5m/15m/30m/1h/4h | May 2026 |
| `figures/btc_walkaway_table.csv` | Walk-away summary table (rendered as a table under `#diligence-settlement`) | Tabulated walk-away stats; lives in `figures/`, not `data/` | May 2026 |
| `DATA.md` | — (data dictionary) | Documents the above; not rendered | — |

¹ Footnote — ticket-size chart label bug: The `bot_ticket_size_*` CSV is correct and self-consistent, but the currently rendered ticket-size exhibit in `crypto on the clock.html` is mislabeled — each metric is shifted one column (chart shows Trades-values under "Wallets" and Volume-values under "Trades"; e.g. true "$1 or less" wallet share is 17.2%, not the 14.2% displayed). The CSV is the source of truth; the live chart needs the column-label fix. README should not imply the live ticket-size chart is correct.

Supporting-data note: per-venue wide CSVs, `*_verification.json` files, `binance_1s` .npy, `implied_prob` JSON, and `DATA.md` are inputs/audit artifacts, not separately rendered exhibits.
