# Surf Data

This folder holds CSV files pulled from Surf. We upload each CSV here as we pull it.

## Source-of-truth: file → chart mapping

Maps each data file in this folder to the exhibit it backs in the published report
(`report/crypto on the clock.html`, live at https://ally-pantera.github.io/crypto-on-the-clock/).
Convention throughout is **tidy-long primary** — the `*_long_*` files are the canonical source;
any per-venue **wide** siblings are supporting/pre-pivot and render no chart of their own.

DOM ids below are the actual anchors in the report HTML. Where an exhibit lives inside a
multi-slide carousel, the id is the carousel `<figure>` and the specific slide is named.
Where a figure has no stable id, the enclosing section anchor is given and marked "no figure id".

### A. Weekly venue-mix / volume — Jan–Jun basis, tidy-long

| File | Exhibit / chart | DOM id | Notes |
|---|---|---|---|
| `category_weekly_long_jan_jun_chart.csv` | "Two Venues, Two Different Mixes" — stacked weekly category volume (absolute USD) | `#cat-volume-chart` | 8 categories; Kalshi 26wk (Dec 29 → Jun 22), PM 25wk (Jan 5 → Jun 22). |
| `duration_weekly_long_jan_jun_chart.csv` | Duration-mix weekly stacked bars — "By duration" slide | `#crypto-breakdown-carousel` (slide 1) | Kalshi {15m, 60m, Longer}, PM {5m, 15m, 1h, 4h, Longer}. Backs the 94% / 79% short-term shares. |
| `asset_weekly_long_jan_jun_chart.csv` | Asset-band weekly stacked bars — "By asset" slide | `#crypto-breakdown-carousel` (slide 2) | BTC / ETH / SOL / XRP / Other. Venue totals reconcile Kalshi $4.48B / PM $5.59B. |

### B. Fee-by-duration — Jan–Jun

| File | Exhibit / chart | DOM id | Notes |
|---|---|---|---|
| `fees_weekly_polymarket_duration_jan_jun_chart.csv` | Polymarket fee-by-duration stacked weekly — "Polymarket by duration (observed)" slide | `#fee-duration-carousel` (slide 1) | Observed per-fill; grand total $53.9M. |
| `fees_weekly_kalshi_duration_jan_jun_chart.csv` | Kalshi fee-by-duration stacked weekly — "Kalshi by duration (modeled)" slide | `#fee-duration-carousel` (slide 2) | Modeled basis; grand total $122.1M (2.8× multiple). |

### C. Trade-size — Jan 1 – May 28 (by design; **not** June)

| File | Exhibit / chart | DOM id | Notes |
|---|---|---|---|
| `trade_size_distribution_long_jan_may_chart.csv` | "Trade-size distribution by venue" — violin / box + percentile dots (Microstructure) | no figure id — violin `<svg>` under section `#head-to-head` | 16 percentile + 62 histogram rows/venue. PM median $3.05 / Kalshi $4.70. The `jan_may` window is correct and intentional (full-tape snapshot ending May 28). |

### D. Bot behavior — 5-minute Polymarket takers, Feb 12 – May 28

| File | Exhibit / chart | DOM id | Notes |
|---|---|---|---|
| `bot_behavior_5m_taker_long_feb_may_chart.csv` | tidy-long master feeding all 4 bot exhibits | — (source table, no standalone chart) | 0-diff superset of the four bot CSVs below. |
| `bot_classification_5m_taker_shares_feb_may_chart.csv` | Trader classification — wallet / trade / volume share | no figure id — figure under section `#diligence-automation` | Bot-like 25.7% / 87.9% / 86.3% (wallets / trades / volume). |
| `bot_ticket_size_5m_taker_feb_may_chart.csv` | Ticket-size distribution — "Ticket size" slide | `#tells-carousel` (slide 1) | ⚠️ **CSV is correct; published chart is column-shifted** — see footnote [¹]. Do **not** "fix" the CSV. |
| `bot_cadence_5m_taker_feb_may_chart.csv` | Trade cadence — "Cadence" slide | `#tells-carousel` (slide 2) | "1s or less" = 84.0% of volume. |
| `bot_concentration_5m_taker_feb_may_chart.csv` | Wallet concentration — "Concentration" slide | `#tells-carousel` (slide 3) | Top-1K 43.3%, Top-10K 73.1% of volume. |

### E. Settlement-manipulation study (`settlement_study/`)

All settlement exhibits render inside the report section anchored `#diligence-settlement`.
The individual figures within that section have no stable id (marked "no figure id" below).

| File | Exhibit / chart | DOM id | Notes |
|---|---|---|---|
| `settlement_study/q3_profiles_within_cycle.json` | Within-cycle volume profile — the 12–17× settlement-spike exhibit | `#diligence-settlement` (no figure id) | 5m ATM peak/median 17.15×. |
| `settlement_study/q3_pricecurve_5m_15m.json` | Jump-and-revert price curve | `#diligence-settlement` (no figure id) | Peaks +1.59 bps (5m) at close, then reverts. |
| `settlement_study/implied_prob/*.json` (5) | Backs the profiles / pricecurve + the 2.9% / 0.9% still-even share stat | `#diligence-settlement` (supporting) | Still-even counts 261 (5m) / 27 (15m) = n_atm. |
| `settlement_study/klines_walkaway/*.json` (5) | "Walkaway" horizon analysis (5m / 15m / 30m / 1h / 4h) + summary table | `#diligence-settlement` (no figure id) | — |
| `settlement_study/figures/btc_walkaway_table.csv` | Walkaway summary table | `#diligence-settlement` | Rendered as a **table**, not a chart. |
| `settlement_study/binance_1s/*.npy` (3) | Raw 1s BTC OHLCV + taker source for the above | — | **Supporting / raw — no standalone chart.** Binary NumPy; do not alter. |
| `settlement_study/DATA.md` | Documentation for the settlement-study data tree | — | Reference only, not rendered. |

---

**Notes**

- **Tidy-long primary:** the `*_long_*` files are canonical. Any per-venue **wide** siblings (e.g. `asset_weekly_kalshi_*` / `asset_weekly_polymarket_*`, `trade_size_distribution_percentiles_*` / `_histogram_*`) are supporting/pre-pivot and render **no chart of their own** — they are not the source of truth and, where present, mirror the long file.
- **Raw / supporting, no rendered chart:** `settlement_study/binance_1s/*.npy` (raw 1s source), `settlement_study/DATA.md`, and any per-venue wide siblings.

[¹] **Ticket-size footnote:** `bot_ticket_size_5m_taker_feb_may_chart.csv` is correct as delivered. The *published* chart is column-shifted (Trades rendered under the Wallets header, Volume under the Trades header). This is a rendering issue in the exhibit, **not** a data error — the CSV must stay as-is.
