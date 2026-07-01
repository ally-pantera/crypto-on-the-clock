# Datasets

Raw and intermediate data backing the study. ~114 MB total. All timestamps are **UTC**.

```
data/
├── klines_walkaway/             BTC/USDT candles for the walk-away analysis (surf → Binance)
├── binance_1s/                  BTC/USDT 1-second spot for the footprint (Binance direct)
├── implied_prob/                per-cycle Polymarket implied probability near close (surf)
├── q3_pricecurve_5m_15m.json    sign-aligned price curve for the report's price chart (derived)
└── q3_profiles_within_cycle.json  per-second within-cycle volume & price profiles (derived)
```

**`q3_profiles_within_cycle.json`** — output of `../compute_profiles.py`; the mean per-second
within-cycle profiles behind the report's four "spiky" charts. Structure: `5m` and `15m`, each
with `D` (cycle length s), `vol_atm`/`vol_dec` (mean Binance spot volume BTC/s by second into
cycle), `ret_atm`/`ret_dec` (mean |1s price move| bps by second), and `n_atm`/`n_dec` cycle counts
(5m: 261/7692; 15m: 27/2667). "atm" = still-even, "dec" = already-decided.

**`q3_pricecurve_5m_15m.json`** — output of `../compute_pricecurve.py`; the mean sign-aligned
Binance price path around the close (push-direction-oriented), used to draw the report's
"price jump-and-revert" SVG. Keys: `xs` (seconds relative to close, −60..+30), `5m` / `15m`
(mean deviation in bps at each second), `5m_n` / `15m_n` (cycle counts: 261 / 27).

---

## 1. `klines_walkaway/btc_{5m,15m,30m,1h,4h}.json`

BTC/USDT **spot** OHLCV, one file per interval. Source: **surf** `exchange-klines`
(`--exchange binance`). Window: **2025-12-01 → 2026-06-01**.

JSON array of candle objects:

| field | type | meaning |
|---|---|---|
| `timestamp` | int | candle **open** time, Unix seconds |
| `open`,`high`,`low`,`close` | float | price (USDT) |
| `volume` | float | volume in **BTC** (base units) |

Row counts: 5m ≈ 52,416 · 15m ≈ 17,472 · 30m ≈ 8,736 · 1h ≈ 4,368 · 4h ≈ 1,092.
Walk-away move per cycle = `|close − open| / open`.

```python
import json, pandas as pd
df = pd.DataFrame(json.load(open("klines_walkaway/btc_5m.json")))
```

---

## 2. `binance_1s/btc_1s_{head,may,tail}.npy`

BTC/USDT **1-second** spot. Source: **Binance direct** (`data-api.binance.vision`,
`/api/v3/klines?interval=1s`). Three contiguous pieces — concatenate for full May:

| file | window |
|---|---|
| `btc_1s_head.npy` | 2026-05-01 → 2026-05-05 |
| `btc_1s_may.npy`  | 2026-05-05 → 2026-05-19 |
| `btc_1s_tail.npy` | 2026-05-19 → 2026-06-01 00:05 |

Each is a **numpy float64 array, shape (N, 5)**. Columns:

| col | name | meaning |
|---|---|---|
| 0 | `ts` | second, Unix seconds (candle open) |
| 1 | `close` | price (USDT) at that second |
| 2 | `volume` | BTC traded that second |
| 3 | `trades` | number of trades that second |
| 4 | `taker_buy_base` | BTC bought by the **taker** (aggressor) that second |

Derived: **signed flow** = `2*taker_buy_base − volume` (net aggressor buying);
**1-second return** = `close.pct_change()`. Combined ≈ 2.68M rows (gap-free except
seconds with zero trades, which the analysis fills with volume 0 / forward-filled price).

```python
import numpy as np
A = np.concatenate([np.load(f"binance_1s/btc_1s_{p}.npy") for p in ("head","may","tail")])
ts, close, volume, trades, taker_buy = A.T
```

---

## 3. `implied_prob/btc{5m,15m,4h}_implied_*.json`

Per **cycle**, the Polymarket implied probability near close. Source: **surf**
`onchain-sql` on `agent.polymarket_trades` ⋈ `agent.polymarket_market_details`.
Files split by duration and date range (5m is in three pieces; concatenate):

| file | duration | window | cycles |
|---|---|---|---|
| `btc5m_implied_head_may01-05.json` | 5m | May 1–5 | 1,141 |
| `btc5m_implied_mid_may05-19.json`  | 5m | May 5–19 | 4,005 |
| `btc5m_implied_tail_may19-jun01.json` | 5m | May 19–Jun 1 | 3,708 |
| `btc15m_implied_may.json` | 15m | full May | 2,873 |
| `btc4h_implied_may.json` | 4h | full May | 60 |

JSON array of per-cycle objects:

| field | type | meaning |
|---|---|---|
| `slug` | str | `btc-updown-{dur}-<openUnix>`; the trailing int = cycle **open/strike** (Unix s). Close = open + duration. |
| `implied_up` | float | P(up) implied by the **last trade in the final 60 s** before close. `= price` if the trade was on the `Up` token, else `1-price`. |
| `pm_vol_last60` | float | Polymarket USD volume in the final 60 s |
| `n60` | int | number of trades in the final 60 s |

Cycle state: **ATM/still-even** = `implied_up ∈ [0.40, 0.60]`; **decided** = `≤0.10` or `≥0.90`.
Cycles with no final-60s trade are absent (couldn't be classified).

```python
import json
cyc = json.load(open("implied_prob/btc5m_implied_mid_may05-19.json"))
open_ts = int(cyc[0]["slug"].split("-")[-1])   # cycle open; close = open_ts + 300
```

---

## Provenance notes
- surf CEX klines floor at 1-minute granularity → the 1-second footprint data had to come
  from Binance directly (main `api.binance.com` returns HTTP 451 from US IPs; the
  `data-api.binance.vision` market-data mirror is open).
- `onchain-sql` caps results at 1000 rows, so the implied-prob pulls are chunked by day
  then concatenated.
- Pull scripts: `../../surf_pull/pull_btc_klines.py`, `pull_binance_1s*.py`,
  `pull_implied*.py` (see `../README.md` §4).
