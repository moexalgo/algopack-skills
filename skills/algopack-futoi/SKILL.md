---
name: algopack-futoi
description: ALGOPACK FUTOI futures open-interest workflows and interpretation. Use when a user asks for futures open interest, FIZ/YUR segmentation, long and short participant counts, pos/pos_long/pos_short fields, aggregate-by-underlying caveats, FO examples, Market("FO").futoi, Ticker(...).futoi, or direct FUTOI endpoints.
---

# ALGOPACK FUTOI

## Overview

Use this skill for futures open interest by participant group. FUTOI is available on the futures market (`FO`) and returns DataFrames by default through `moexalgo`.

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

## Interpretation Rules

- `clgroup` is `FIZ` for individuals and `YUR` for legal entities.
- `pos` is net position; `pos_long` and `pos_short` are gross long/short open positions.
- `pos_long_num` and `pos_short_num` count participants with long/short positions.
- FUTOI is aggregated by underlying/instrument, not necessarily by a single contract series.
- A participant can be counted in both long and short groups if they hold long positions in one contract and short positions in another.

## References

Read `references/futoi.md` when you need:

- Endpoint map and field descriptions.
- FIZ/YUR methodology and aggregation caveats.
- DataFrame groupby/pivot analysis examples.
- Direct REST examples for unsupported library scenarios.

## Boundary

Do not infer individual trader behavior from aggregate FUTOI rows. Explain limitations when asked about specific participants or exact contract-level FIZ/YUR splits.
