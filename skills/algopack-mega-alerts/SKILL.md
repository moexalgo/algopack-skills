---
name: algopack-mega-alerts
description: Direct ALGOPACK Mega Alerts API workflows for market anomaly alerts, alert_type thresholds, reference JSON, EQ/FO alert endpoints, curl, JSON/CSV, abnormal volume or price signals, and chart-ready anomaly tables.
---

# ALGOPACK Mega Alerts

## Overview

Use this skill for direct Mega Alerts endpoint access. Alerts identify unusual market activity and include historical context in the `reference` field.

## Quick Start

```bash
curl -L "https://apim.moex.com/iss/datashop/algopack/eq/alerts/SBER.json?from=2025-01-01&till=2025-03-30" \
  -H "Authorization: Bearer ${APIKEY}"
```

Use `date=YYYY-MM-DD` for market-wide alerts and `from=YYYY-MM-DD&till=YYYY-MM-DD` for one ticker.

## Core Workflow

1. Choose market `eq` or `fo` unless current entitlement proves more coverage.
2. Fetch alerts and parse the `data` block.
3. Group by `alert_type`, `secid`, and date/time.
4. Parse `reference` only as historical post-alert context.
5. Avoid forecast language.

## References

Read `references/mega-alerts.md` for endpoints, alert types, fields, `reference` schema, and chart recipes.

## Boundary

Do not present alerts as predictions or recommendations. Phrase results as observed anomalies and historical post-alert statistics.
