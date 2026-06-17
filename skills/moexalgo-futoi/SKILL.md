---
name: moexalgo-futoi
description: Python moexalgo library workflows for FUTOI DataFrames, session.TOKEN, Market("FO").futoi, Ticker(...).futoi, futures open interest, FIZ/YUR segmentation, pos and pos_long or pos_short fields, participant counts, pivots, groupbys, and aggregation caveats.
---

# MOEXAlgo FUTOI

## Overview

Use this skill when the user wants FUTOI through the `moexalgo` Python package and DataFrame analysis.

## Quick Start

```python
import os
from moexalgo import session, Market, Ticker

session.TOKEN = os.environ["APIKEY"]

fo = Market("FO")
all_futoi = fo.futoi(date="2025-01-10")

si = Ticker("SiH5")
si_futoi = si.futoi(start="2025-01-10", end="2025-01-10")
```

## Core Workflow

1. Use `Market("FO").futoi(date=...)` for all futures FUTOI rows.
2. Use `Ticker(...).futoi(start=..., end=...)` for one futures instrument.
3. Split by `clgroup`, usually `FIZ` and `YUR`.
4. Use pivots/groupbys for net, gross, and participant-count charts.
5. Explain that rows are aggregate snapshots, not individual trader records.

## References

Read `references/futoi.md` for signatures, fields, aggregation caveats, and DataFrame examples.

## Boundary

For browser URLs, curl, raw JSON/CSV, or no-Python chart recipes, use the direct FUTOI skill instead.
