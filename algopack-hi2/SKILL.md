---
name: algopack-hi2
description: ALGOPACK HI2/HHI market concentration workflows and interpretation. Use when a user asks for Herfindahl-Hirschman concentration metrics, HI2 daily cadence, interpretation bands, hhi_* metric names, Market.hi2 or Ticker.hi2 examples, market/ticker examples for EQ/FO/FX, or concentration analysis in DataFrames.
---

# ALGOPACK HI2

## Overview

Use this skill for ALGOPACK HI2 market concentration data. HI2 values are Herfindahl-Hirschman style concentration metrics calculated daily for supported EQ, FO, and FX instruments.

## Quick Start

```python
import os
from moexalgo import session, Market, Ticker

session.TOKEN = os.environ["APIKEY"]

eq = Market("EQ")
hi2_market = eq.hi2(date="2025-01-10")

sber = Ticker("SBER")
hi2_sber = sber.hi2(start="2025-01-01", end="2025-01-31")
```

## Interpretation Bands

- `< 1500`: low concentration / more competitive participation.
- `1500-2500`: moderate concentration.
- `> 2500`: high concentration / a few participants dominate the metric.

Treat these as screening bands, not trading rules.

## References

Read `references/hi2.md` when you need:

- Formula, cadence, coverage, and unsupported cases.
- Full metric names and meanings.
- Endpoint map for EQ, FO, and FX.
- DataFrame examples for pivoting and banding HI2 values.

## Boundary

If HI2 rows are missing for an instrument/date, explain that calculation may be unavailable when participant counts are below the product threshold or when the instrument is outside coverage.
