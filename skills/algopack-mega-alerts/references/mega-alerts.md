# Mega Alerts Reference

## Contents

- [Scope](#scope)
- [DataFrame Calls](#dataframe-calls)
- [Direct Endpoints](#direct-endpoints)
- [Fields](#fields)
- [Alert Types](#alert-types)
- [Threshold Methodology](#threshold-methodology)
- [Reference JSON](#reference-json)
- [Analysis Snippets](#analysis-snippets)

## Scope

Mega Alerts identify unusual market activity from minute-level data. Local docs describe monitoring over liquid instruments and a 90-day rolling window for thresholds. Documented direct endpoints list EQ and FO; if FX behavior matters, verify the raw endpoint and entitlement before promising coverage.

## DataFrame Calls

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

In `moexalgo` DataFrames, raw ISS `secid` is normalized to `ticker`.

## Direct Endpoints

- `/iss/datashop/algopack/eq/alerts[/{ticker}].json`
- `/iss/datashop/algopack/fo/alerts[/{ticker}].json`

Only EQ and FO alert endpoints are documented in local endpoint lists. Wiki prose has mentioned FX, but local Q&A confirms the documented API values are EQ/FO. A live FX route may respond but still lack populated coverage, so verify rows and entitlement before promising FX alerts.

For market-wide calls, use `date=YYYY-MM-DD`. For ticker calls, use `from=YYYY-MM-DD&till=YYYY-MM-DD`.

## Fields

| Field | Meaning |
| --- | --- |
| `tradedate` | Date |
| `tradetime` | Alert time |
| `ticker` | Instrument id in `moexalgo` DataFrames; raw ISS uses `secid` |
| `alert_type` | Anomaly code |
| `threshold` | Trigger threshold |
| `value` | Observed value at alert time |
| `reference` | JSON string with historical post-alert statistics |
| `systime` | Load/system time |

## Alert Types

Volume:

- `vol_s_99_9_pctl`: sell volume exceeded 99.9 percentile.
- `vol_b_99_9_pctl`: buy volume exceeded 99.9 percentile.
- `vol_99_9_pctl`: total volume exceeded 99.9 percentile.
- `vol_s_max`, `vol_b_max`, `vol_max`: 90-day historical max events.

Net volume:

- `net_vol_99_9_pctl-`: negative net volume exceeded threshold.
- `net_vol_99_9_pctl+`: positive net volume exceeded threshold.
- `net_vol_min`, `net_vol_max`: historical min/max net volume events.

Price change:

- `pr_change_99_9_pctl-`: unusually large price decrease.
- `pr_change_99_9_pctl+`: unusually large price increase.
- `pr_change_min`, `pr_change_max`: historical min/max price-change events.

Price levels:

- `pr_high_max`: 90-day high reached.
- `pr_low_min`: 90-day low reached.

## Threshold Methodology

- `_99_9_pctl` alerts compare the current minute metric with a 99.9 percentile threshold from recent historical minute data.
- `_min` and `_max` alerts compare current values with recent historical extremes.
- Thresholds are recalculated from a rolling historical window, documented locally as 90 days.

## Reference JSON

`reference` is a JSON string. After `json.loads`, local examples show a list with one object:

```json
[{
  "m_5": ["0.086", "-0.078", "25", "18", "0.017"],
  "m_15": ["0.134", "-0.101", "22", "21", "0.019"],
  "m_30": ["0.161", "-0.138", "22", "20", "0.018"],
  "h_1": ["0.215", "-0.146", "18", "26", "0.002"],
  "vol_b": 442006,
  "vol_s": 53039
}]
```

For `m_5`, `m_15`, `m_30`, `h_1`, the five positions are:

1. Average price change when price rose, percent.
2. Average price change when price fell, percent.
3. Count of rising cases.
4. Count of falling cases.
5. Average change across all observations, percent.

This is historical context after similar anomalies, not a prediction.

## Analysis Snippets

Safely parse references:

```python
import json
import pandas as pd

def parse_reference(value):
    if not value:
        return {}
    try:
        data = json.loads(value)
    except (TypeError, json.JSONDecodeError):
        return {}
    return data[0] if data else {}

refs = alerts["reference"].map(parse_reference)
ref_df = pd.json_normalize(refs)
combined = alerts.join(ref_df.add_prefix("ref_"))
```

Summarize alert frequency:

```python
counts = alerts.groupby(["ticker", "alert_type"]).size().sort_values(ascending=False)
```

Inspect strongest threshold breaches:

```python
alerts["breach_abs"] = (alerts["value"] - alerts["threshold"]).abs()
top = alerts.sort_values("breach_abs", ascending=False).head(20)
```
