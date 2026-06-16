# HI2 Reference

## Contents

- [Meaning and Cadence](#meaning-and-cadence)
- [DataFrame Calls](#dataframe-calls)
- [Direct Endpoints](#direct-endpoints)
- [Fields](#fields)
- [Metric Names](#metric-names)
- [Interpretation Bands](#interpretation-bands)
- [Analysis Snippets](#analysis-snippets)

## Meaning and Cadence

HI2 is a Herfindahl-Hirschman style concentration index:

```text
HI2 = sum(s_i ** 2)
```

where `s_i` is a participant share in percent. Example: shares 50, 30, and 20 produce `2500 + 900 + 400 = 3800`.

Documented cadence is daily near the end of the trading day. Coverage includes supported EQ, FO, and FX instruments. If too few participants trade an instrument, HI2 may not be calculated for that date.

## DataFrame Calls

```python
import os
from moexalgo import session, Market, Ticker

session.TOKEN = os.environ["APIKEY"]

eq = Market("EQ")
market_hi2 = eq.hi2(date="2025-01-10")

sber = Ticker("SBER")
sber_hi2 = sber.hi2(start="2025-01-01", end="2025-01-31")
```

In `moexalgo` DataFrames, raw ISS `secid` is normalized to `ticker`.

## Direct Endpoints

- `/iss/datashop/algopack/eq/hi2[/{ticker}].json`
- `/iss/datashop/algopack/fo/hi2[/{ticker}].json`
- `/iss/datashop/algopack/fx/hi2[/{ticker}].json`

For market-wide calls, use `date=YYYY-MM-DD`. For ticker calls, use `from=YYYY-MM-DD&till=YYYY-MM-DD`.

## Fields

| Field | Meaning |
| --- | --- |
| `tradedate` | Date |
| `tradetime` | Metric time |
| `ticker` | Instrument id in `moexalgo` DataFrames; raw ISS uses `secid` |
| `metric` | HI2 metric name |
| `value` | HHI value |
| `reference` | Additional context, often null |
| `systime` | Load/system time |

## Metric Names

Use these canonical names when filtering:

- `hhi_volume`: concentration by trading volume.
- `hhi_buy`: concentration by buy volume.
- `hhi_sell`: concentration by sell volume.
- `hhi_netflow_buy`: concentration by positive net flow.
- `hhi_netflow_sell`: concentration by negative net flow.
- `hhi_passive`: concentration by maker/passive volume.
- `hhi_active` or `hhi_aggressive`: concentration by taker/aggressive volume. Local docs use both naming variants; verify actual DataFrame values with `df["metric"].unique()`.
- `hhi_passive_buy`, `hhi_passive_sell`.
- `hhi_active_buy`, `hhi_active_sell` or `hhi_aggressive_buy`, `hhi_aggressive_sell`.

## Interpretation Bands

| Value | Interpretation |
| --- | --- |
| `< 1500` | Lower concentration, more competitive distribution |
| `1500-2500` | Moderate concentration |
| `> 2500` | High concentration, fewer dominant participants |

These are broad screening bands. Do not turn them into trading recommendations.

## Analysis Snippets

Band HI2 rows:

```python
def concentration_band(value):
    if value < 1500:
        return "low"
    if value <= 2500:
        return "moderate"
    return "high"

df = sber.hi2(start="2025-01-01", end="2025-01-31")
df["band"] = df["value"].map(concentration_band)
```

Pivot metrics by date:

```python
pivot = df.pivot_table(index="tradedate", columns="metric", values="value", aggfunc="last")
```

Find concentrated instruments:

```python
market = eq.hi2(date="2025-01-10")
high = market[(market["metric"] == "hhi_volume") & (market["value"] > 2500)]
```
