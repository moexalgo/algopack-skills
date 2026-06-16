---
name: algopack-supercandles
description: ALGOPACK SuperCandles workflows for tradestats, orderstats, and obstats on EQ, FO, and FX markets. Use when a user asks for 5-minute trade/order/order-book statistics, VWAP, imbalance, liquidity, spread, order flow metrics, SuperCandles endpoint fields, or DataFrame examples with Market.tradestats/orderstats/obstats and Ticker.tradestats/orderstats/obstats.
---

# ALGOPACK SuperCandles

## Overview

Use this skill for calculated 5-minute ALGOPACK metrics from trades, orders, and order books. Prefer `moexalgo` DataFrame calls and add interpretation only when the user asks or when it clarifies a metric.

## Quick Start

```python
import os
from moexalgo import session, Market, Ticker

session.TOKEN = os.environ["APIKEY"]

eq = Market("EQ")
today_trade_stats = eq.tradestats(date="2025-01-10")
latest_ob = eq.obstats(date="2025-01-10", latest=True)

sber = Ticker("SBER")
trades = sber.tradestats(start="2025-01-01", end="2025-01-31")
orders = sber.orderstats(start="2025-01-01", end="2025-01-31")
book = sber.obstats(start="2025-01-01", end="2025-01-31")
```

## Method Choice

- Use `tradestats` for price, volume, trade count, buy/sell split, `disb`, and VWAP.
- Use `orderstats` for placed/cancelled orders and order-flow VWAP.
- Use `obstats` for order-book liquidity, spreads, levels, imbalance, and book VWAP.
- Use `Market("EQ"|"FO"|"FX")` for all instruments by date; use `Ticker(...)` for one instrument over `start`/`end`.
- Remember that SuperCandles publish every 5 minutes and may appear several seconds after an interval closes.

## References

Read `references/supercandles.md` when you need:

- Metric interpretation and formula hints.
- EQ/FO/FX endpoint map and availability.
- Field descriptions for TradeStats, OrderStats, and OBStats.
- DataFrame analysis examples for VWAP, imbalance, and liquidity.

## Boundary

Present SuperCandles as data for research, monitoring, and strategy development. Do not represent metric thresholds or imbalances as trading advice.
