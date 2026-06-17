---
name: moexalgo-market-data
description: Python moexalgo library workflows for Market and Ticker market data, session.TOKEN, pandas DataFrames, tickers, marketdata, info, candles, trades, orderbook, market aliases, board overrides, field selection, native mode, and basic EQ/FO/FX data analysis.
---

# MOEXAlgo Market Data

## Overview

Use this skill when the user wants the `moexalgo` Python package, pandas-style DataFrames, or `Market`/`Ticker` helpers for market data.

## Quick Start

```python
import os
from moexalgo import session, Market, Ticker

session.TOKEN = os.environ["APIKEY"]

eq = Market("EQ")
tickers = eq.tickers("ticker", "shortname", "minstep")
quotes = eq.marketdata("ticker", "last", "bid", "offer", "updatetime")

sber = Ticker("SBER")
info = sber.info("ticker", "title", "market", "engine", "decimals")
candles = sber.candles(start="2025-01-01", end="2025-01-31", period="1h")
trades = sber.trades(latest=True)
book = sber.orderbook()
```

## Core Workflow

1. Set `session.TOKEN` from an environment variable when subscriber access is needed.
2. Use `Market("EQ"|"FO"|"FX")` for market-wide rows and `Ticker("SBER")` for one instrument.
3. Pass explicit field names for compact DataFrames; pass `"*"` for schema discovery.
4. Use `board=...` only after metadata shows a non-default board is required.
5. Use `native=True` only when the user wants iterators/dicts instead of DataFrames.

## References

Read `references/market-data.md` for signatures, aliases, fields, candle periods, board caveats, and DataFrame examples.

## Boundary

For raw REST URLs, curl, ISS pagination mechanics, or non-Python integrations, use the direct ISS/API skills instead.
