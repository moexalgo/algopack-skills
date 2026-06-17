---
name: algopack-hi2
description: Direct ALGOPACK HI2 API workflows for Herfindahl-Hirschman market concentration, hhi_* metrics, daily EQ/FO/FX concentration rows, raw hi2 endpoints, curl, JSON/CSV, interpretation bands, and chart-ready concentration tables.
---

# ALGOPACK HI2

## Overview

Use this skill for direct HI2 endpoint access and concentration analysis. HI2 is a Herfindahl-Hirschman style metric calculated for supported EQ, FO, and FX instruments.

## Quick Start

```bash
curl -L "https://apim.moex.com/iss/datashop/algopack/eq/hi2/SBER.json?from=2025-01-01&till=2025-01-31" \
  -H "Authorization: Bearer ${APIKEY}"
```

Use `date=YYYY-MM-DD` for all instruments and `from=YYYY-MM-DD&till=YYYY-MM-DD` for one ticker.

## Core Workflow

1. Choose market `eq`, `fo`, or `fx`.
2. Fetch all instruments by `date` or one ticker by `from`/`till`.
3. Filter by `metric`, usually `hhi_volume` first.
4. Apply broad bands: below `1500`, `1500-2500`, above `2500`.
5. Explain missing rows as possible coverage, date, entitlement, or participant-threshold issues.

## References

Read `references/hi2.md` for formula, endpoints, metrics, fields, interpretation bands, and chart recipes.

## Boundary

Treat HI2 as a screening and market-structure metric, not as a buy/sell signal.
