---
name: algopack-futoi
description: Direct ALGOPACK FUTOI API workflows for futures open interest, FIZ/YUR segmentation, long and short participant counts, raw futoi endpoints, browser URLs, curl, JSON/CSV, aggregation caveats, and chart-ready FIZ/YUR tables without Python setup.
---

# ALGOPACK FUTOI

## Overview

Use this skill for direct FUTOI endpoint access and quick FIZ/YUR open-interest charts. FUTOI is futures open interest split by participant group and published as snapshots.

## Quick Start

```bash
curl -L "https://apim.moex.com/iss/analyticalproducts/futoi/securities/Si.json?from=2025-01-10&till=2025-01-10" \
  -H "Authorization: Bearer ${APIKEY}"
```

Use `/securities.json?date=YYYY-MM-DD` for all instruments and `/securities/{ticker}.json?from=...&till=...` for one instrument or underlying.

## Core Workflow

1. Use `FO` tickers or underlyings only.
2. Fetch rows for a date/range and parse the `futoi` block.
3. Split by `clgroup`: `FIZ` for individuals, `YUR` for legal entities.
4. Plot `pos`, `pos_long`, `pos_short`, `pos_long_num`, and `pos_short_num`.
5. Explain aggregation caveats before making conclusions about specific contracts or participants.

## References

Read `references/futoi.md` for endpoints, fields, aggregation rules, access caveats, and chart recipes.

## Boundary

Do not infer individual trader behavior or exact single-contract FIZ/YUR positioning from aggregate FUTOI rows.
