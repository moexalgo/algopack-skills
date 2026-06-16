# SuperCandles Reference

## Contents

- [Scope and Cadence](#scope-and-cadence)
- [DataFrame Calls](#dataframe-calls)
- [Direct Endpoint Map](#direct-endpoint-map)
- [TradeStats Fields](#tradestats-fields)
- [OrderStats Fields](#orderstats-fields)
- [OBStats Fields](#obstats-fields)
- [Analysis Snippets](#analysis-snippets)

## Scope and Cadence

SuperCandles are 5-minute calculated metrics based on three streams:

- `tradestats`: trades, prices, volume, buy/sell split, VWAP.
- `orderstats`: placed and cancelled orders, order-flow volumes and values.
- `obstats`: order-book depth, spreads, liquidity, imbalance, and VWAP.

Markets: `EQ`, `FO`, `FX`. History is documented from 2020. Publication is every 5 minutes, usually several seconds after the interval closes.

## DataFrame Calls

```python
import os
from moexalgo import session, Market, Ticker

session.TOKEN = os.environ["APIKEY"]

eq = Market("EQ")
eq_trades = eq.tradestats(date="2025-01-10")
eq_orders = eq.orderstats(date="2025-01-10")
eq_book = eq.obstats(date="2025-01-10")

sber = Ticker("SBER")
sber_trades = sber.tradestats(start="2025-01-01", end="2025-01-31")
sber_orders = sber.orderstats(start="2025-01-01", end="2025-01-31")
sber_book = sber.obstats(start="2025-01-01", end="2025-01-31")
```

Market methods use `date=...`; ticker methods use `start=...`, `end=...`. `latest=True` asks for the latest available rows in the dataset. In `moexalgo` DataFrames, raw ISS `secid` is normalized to `ticker`.

## Direct Endpoint Map

| Market | TradeStats | OrderStats | OBStats |
| --- | --- | --- | --- |
| EQ | `/iss/datashop/algopack/eq/tradestats[/{ticker}].json` | `/iss/datashop/algopack/eq/orderstats[/{ticker}].json` | `/iss/datashop/algopack/eq/obstats[/{ticker}].json` |
| FO | `/iss/datashop/algopack/fo/tradestats[/{ticker}].json` | `/iss/datashop/algopack/fo/orderstats[/{ticker}].json` | `/iss/datashop/algopack/fo/obstats[/{ticker}].json` |
| FX | `/iss/datashop/algopack/fx/tradestats[/{ticker}].json` | `/iss/datashop/algopack/fx/orderstats[/{ticker}].json` | `/iss/datashop/algopack/fx/obstats[/{ticker}].json` |

For all instruments, use `date=YYYY-MM-DD`. For one instrument, use `from=YYYY-MM-DD&till=YYYY-MM-DD`. Use `start` for pagination in raw REST.

## TradeStats Fields

Core fields:

- `tradedate`, `tradetime`, `ticker` in DataFrames (`secid` in raw ISS), `systime`.
- `pr_open`, `pr_high`, `pr_low`, `pr_close`: prices in the 5-minute interval.
- `pr_vwap`: volume-weighted average price, approximately `val / vol`.
- `pr_vwap_b`, `pr_vwap_s`: VWAP for buyer-initiated and seller-initiated trades.
- `pr_change`: percent change from open to close.
- `pr_std`: price standard deviation.
- `trades`, `trades_b`, `trades_s`: total, buy, and sell trade counts.
- `vol`, `vol_b`, `vol_s`: total, buy, and sell volume.
- `val`, `val_b`, `val_s`: total, buy, and sell value.
- `disb`: buy/sell imbalance. Positive means more buyer-initiated activity; negative means more seller-initiated activity.

FO additions can include:

- `asset_code`: underlying asset.
- `im`: initial margin.
- `oi_open`, `oi_high`, `oi_low`, `oi_close`: open interest OHLC.
- `sec_pr_open`, `sec_pr_high`, `sec_pr_low`, `sec_pr_close`: seconds from interval start to price event.

FX differences: volume fields are in currency units rather than share lots or futures contracts; no `im` or `oi_*` fields.

## OrderStats Fields

- `put_orders_b`, `put_orders_s`: placed buy/sell orders.
- `put_vol_b`, `put_vol_s`: placed buy/sell volume.
- `put_val_b`, `put_val_s`: placed buy/sell value.
- `put_vwap_b`, `put_vwap_s`: VWAP of placed buy/sell orders.
- `put_orders`, `put_vol`, `put_val`: totals.
- `cancel_orders_b`, `cancel_orders_s`: cancelled buy/sell orders.
- `cancel_vol_b`, `cancel_vol_s`: cancelled buy/sell volume.
- `cancel_val_b`, `cancel_val_s`: cancelled buy/sell value.
- `cancel_vwap_b`, `cancel_vwap_s`: VWAP of cancelled buy/sell orders.
- `cancel_orders`, `cancel_vol`, `cancel_val`: totals.

Use OrderStats to inspect supply/demand intent and order-flow churn. High placed volume with high cancellation volume can indicate fleeting liquidity.

## OBStats Fields

EQ core:

- `spread_bbo`: best bid-offer spread, usually in basis points.
- `spread_lv10`: spread between 10th bid and 10th ask levels.
- `spread_1mio`: estimated spread for a 1-million-ruble trade.
- `levels_b`, `levels_s`: count of bid/ask price levels.
- `vol_b`, `vol_s`, `val_b`, `val_s`: visible book volume/value.
- `imbalance_vol_bbo`, `imbalance_val_bbo`: BBO imbalance.
- `imbalance_vol`, `imbalance_val`: full-book imbalance.
- `vwap_b`, `vwap_s`: book-side VWAP.
- `vwap_b_1mio`, `vwap_s_1mio`: estimated average bid/ask price for 1 million rubles.

FO/FX can include:

- `mid_price`, `micro_price`.
- `spread_l1`, `spread_l2`, `spread_l3`, `spread_l5`, `spread_l10`, `spread_l20`.
- `vol_b_l1..l20`, `vol_s_l1..l20`: cumulative volume through book depth.
- `vwap_b_l3..l20`, `vwap_s_l3..l20`.

Interpretation:

- Lower spreads generally indicate better immediate liquidity.
- More levels and higher volume/value indicate deeper visible liquidity.
- Positive imbalance means bid-side dominance; negative means ask-side dominance.

## Analysis Snippets

VWAP and buy/sell pressure:

```python
df = sber.tradestats(start="2025-01-01", end="2025-01-31")
df["datetime"] = df["tradedate"].astype(str) + " " + df["tradetime"].astype(str)
pressure = df[["datetime", "ticker", "pr_close", "pr_vwap", "disb", "vol", "trades"]]
```

Order activity:

```python
orders = sber.orderstats(start="2025-01-01", end="2025-01-31")
orders["cancel_to_put"] = orders["cancel_orders"] / orders["put_orders"].replace(0, float("nan"))
```

Book liquidity:

```python
book = sber.obstats(start="2025-01-01", end="2025-01-31")
liquidity = book[["tradedate", "tradetime", "spread_bbo", "imbalance_vol", "vol_b", "vol_s"]]
```
