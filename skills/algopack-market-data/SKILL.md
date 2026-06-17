---
name: algopack-market-data
description: Direct ALGOPACK and MOEX ISS market-data workflows for browser URLs, curl, JSON, CSV, chart-ready quotes, candles, trades, order books, securities metadata, EQ/FO/FX endpoint selection, iss.only filters, and no-library access.
---

# ALGOPACK Market Data

## Overview

Use this skill for direct HTTP access to current quotes, securities metadata, candles, trades, and order books. Default to copyable URLs, `curl`, JSON/CSV parsing, and chart-ready output.

## Quick Start

```bash
curl -L "https://apim.moex.com/iss/engines/stock/markets/shares/boards/TQBR/securities/SBER/candles.json?from=2025-01-01&till=2025-01-31&interval=60" \
  -H "Authorization: Bearer ${APIKEY}"
```

For public delayed ISS data, try the same path on `https://iss.moex.com/iss` without the bearer header. For subscriber real-time access, use `https://apim.moex.com/iss` with `Authorization: Bearer ${APIKEY}`.

## Core Workflow

1. Choose the market route: shares `stock/shares/TQBR`, futures `futures/forts/RFUD`, or currency `currency/selt/CETS`.
2. Choose the block: `securities`, `marketdata`, `candles`, `trades`, or `orderbook`.
3. Add `.json` for programmatic answers or `.csv` when the user wants a spreadsheet/chart feed.
4. Use `iss.only` and column filters for smaller payloads.
5. Use `start` pagination for historical candles or any response that reaches a row limit.

## References

Read `references/market-data.md` for endpoint maps, parameters, field meanings, response parsing, and chart-ready recipes.

## Boundary

ALGOPACK provides data and analytics only. Do not describe it as an order-placement or broker trading API.
