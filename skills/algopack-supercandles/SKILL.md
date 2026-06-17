---
name: algopack-supercandles
description: Direct ALGOPACK SuperCandles API workflows for tradestats, orderstats, obstats, 5-minute trade/order/order-book metrics, VWAP, imbalance, liquidity, spreads, EQ/FO/FX endpoints, curl, JSON/CSV, pagination, and chart-ready answers.
---

# ALGOPACK SuperCandles

## Overview

Use this skill for raw SuperCandles endpoints and chart-ready 5-minute metrics. Return URLs, `curl`, JSON/CSV guidance, and concise metric interpretation.

## Quick Start

```bash
curl -L "https://apim.moex.com/iss/datashop/algopack/eq/tradestats/SBER.json?from=2025-01-01&till=2025-01-31&start=0" \
  -H "Authorization: Bearer ${APIKEY}"
```

Use `date=YYYY-MM-DD` for all instruments and `from=YYYY-MM-DD&till=YYYY-MM-DD` for one ticker.

## Core Workflow

1. Pick the dataset: `tradestats` for trades/VWAP, `orderstats` for order flow, or `obstats` for book liquidity.
2. Pick the market prefix: `eq`, `fo`, or `fx`.
3. Use `latest=1` for the newest rows or `start` pagination for full days/ranges.
4. Normalize raw `secid` to `ticker` in user-facing tables.
5. For charts, keep time, ticker, close/VWAP, volume, spread, imbalance, and relevant buy/sell split columns.

## References

Read `references/supercandles.md` for endpoint maps, fields, interpretation notes, limits, and chart recipes.

## Boundary

Describe SuperCandles as analytics for research and monitoring. Do not turn imbalance or threshold examples into trading advice.
