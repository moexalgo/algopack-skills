---
name: algopack-market-data
description: DataFrame-first MOEXAlgo workflows for ALGOPACK real-time market data and instrument metadata. Use when a user asks for Market or Ticker usage, market aliases and boards, tickers, marketdata, info, candles, trades, orderbook, field selection like eq.tickers("minstep") or eq.tickers("*"), or basic EQ/FO/FX data retrieval with the moexalgo Python package.
---

# ALGOPACK Market Data

## Overview

Use this skill for `moexalgo` market and ticker data before reaching for raw REST. Return pandas-readable DataFrames by default and only use `native=True` when the user explicitly asks for raw iterators or dictionaries.

## Quick Start

```python
import os
from moexalgo import session, Market, Ticker

session.TOKEN = os.environ["APIKEY"]

eq = Market("EQ")
tickers = eq.tickers("ticker", "shortname", "minstep")
all_fields = eq.tickers("*")
market = eq.marketdata("ticker", "last", "bid", "offer")

sber = Ticker("SBER")
info = sber.info("ticker", "market", "engine", "decimals")
candles = sber.candles(start="2025-01-01", end="2025-01-31", period="1h")
book = sber.orderbook()
```

## Selection Rules

- Use `Market("EQ")` for shares, `Market("FO")` for futures, and `Market("FX")` for currency.
- Use `Ticker("SBER")` when the user wants one instrument and `Market(...)` when they want all instruments for a market.
- Pass explicit fields to reduce DataFrame width. Use `"*"` when the user asks for all fields or schema discovery.
- Use `Ticker(..., board="TQBR")` or `Market(..., board="RFUD")` only when a non-default board is required.
- Mention that current trades are for the current trading day, while candles can take historical `start` and `end`.

## References

Read `references/market-data.md` when you need:

- Market aliases, boards, and supported methods.
- Default and all-field behavior for `tickers` and `marketdata`.
- Candle periods, common method signatures, and endpoint mapping.
- Field meanings for marketdata, candles, trades, and orderbook.

## Boundary

Do not imply that ALGOPACK places orders. For order routing, direct the user to broker APIs.
