# MOEXAlgo HI2 Reference

## Imports and Auth

```python
import os
from moexalgo import session, Market, Ticker

session.TOKEN = os.environ["APIKEY"]
```

HI2 usually requires subscriber entitlement.

## Meaning and Cadence

HI2 is a Herfindahl-Hirschman style concentration index:

```text
HI2 = sum(s_i ** 2)
```

where `s_i` is a participant share in percent. Local docs describe daily calculation for supported EQ, FO, and FX instruments. If too few participants trade an instrument, a row may not be calculated.

## Method Signatures

```text
Market(...).hi2(date=None, latest=None, offset=None, native=False)
Ticker(...).hi2(start=None, end=None, latest=None, offset=None, native=False)
```

Pass explicit dates. In the current repo code, omitting `date` on market methods or `start`/`end` on ticker methods reaches `prepare_from_till_dates(...)` and raises instead of silently using today. Market methods request all instruments for one date. Ticker methods request one instrument over a `start`/`end` range. Use `latest=True` when the latest available rows are enough.

## Examples

Market-wide:

```python
eq = Market("EQ")
hi2 = eq.hi2(date="2025-01-10")
```

Ticker range:

```python
sber = Ticker("SBER")
hi2 = sber.hi2(start="2025-01-01", end="2025-01-31")
```

Futures and FX:

```python
fo_hi2 = Market("FO").hi2(date="2025-01-10")
fx_hi2 = Market("FX").hi2(date="2025-01-10")
```

## Fields

| Field | Meaning |
| --- | --- |
| `tradedate` | Date |
| `tradetime` | Metric time |
| `ticker` | Instrument id normalized by the library |
| `metric` | HI2 metric name |
| `value` | HHI value |
| `reference` | Additional context, often null |
| `systime` | Load/system time |

## Metric Names

Use returned values from `df["metric"].unique()` when precision matters. Common names:

- `hhi_volume`: concentration by trading volume.
- `hhi_buy`: concentration by buy volume.
- `hhi_sell`: concentration by sell volume.
- `hhi_netflow_buy`: concentration by positive net flow.
- `hhi_netflow_sell`: concentration by negative net flow.
- `hhi_passive`: concentration by passive/maker volume.
- `hhi_active` or `hhi_aggressive`: concentration by active/taker volume.
- `hhi_passive_buy`, `hhi_passive_sell`.
- `hhi_active_buy`, `hhi_active_sell` or `hhi_aggressive_buy`, `hhi_aggressive_sell`.

## Interpretation Bands

```python
def concentration_band(value):
    if value < 1500:
        return "low"
    if value <= 2500:
        return "moderate"
    return "high"
```

Treat these as broad screening bands, not trading rules.

## DataFrame Analysis

Band rows:

```python
df = Ticker("SBER").hi2(start="2025-01-01", end="2025-01-31")
df["band"] = df["value"].map(concentration_band)
```

Pivot metrics by date:

```python
pivot = df.pivot_table(index="tradedate", columns="metric", values="value", aggfunc="last")
```

Find concentrated instruments:

```python
market = Market("EQ").hi2(date="2025-01-10")
high = market[(market["metric"] == "hhi_volume") & (market["value"] > 2500)]
```

Metric availability check:

```python
available_metrics = sorted(df["metric"].dropna().unique())
```
