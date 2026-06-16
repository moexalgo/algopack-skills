---
name: algopack-mega-alerts
description: ALGOPACK Mega Alerts workflows for market anomaly alerts and reference JSON analysis. Use when a user asks for alert types, thresholds, abnormal price/volume/net-volume signals, parsing the reference field, DataFrame analysis of alerts, Market.alerts or Ticker.alerts examples, or raw alerts endpoints.
---

# ALGOPACK Mega Alerts

## Overview

Use this skill for ALGOPACK anomaly alerts. Alerts identify unusual market activity from minute-level data and include a `reference` JSON string with historical post-alert behavior.

## Quick Start

```python
import json
import os
from moexalgo import session, Market, Ticker

session.TOKEN = os.environ["APIKEY"]

eq = Market("EQ")
alerts = eq.alerts(date="2025-01-10")

sber = Ticker("SBER")
sber_alerts = sber.alerts(start="2025-01-01", end="2025-03-30")

parsed = sber_alerts["reference"].dropna().map(json.loads)
```

## Interpretation Rules

- `threshold` is the trigger threshold, often a 99.9 percentile or historical min/max threshold.
- `value` is the observed value at alert time.
- `reference` is historical context after similar alerts, not a forecast.
- Use alert suffixes: `+` for positive/upside direction and `-` for negative/downside direction.

## References

Read `references/mega-alerts.md` when you need:

- Alert type map and threshold methodology.
- `reference` JSON schema and parsing examples.
- Endpoint map and field descriptions.
- DataFrame examples for summarizing alerts.

## Boundary

Do not present alerts as predictions or recommendations. Phrase results as observed anomalies and historical post-alert statistics.
