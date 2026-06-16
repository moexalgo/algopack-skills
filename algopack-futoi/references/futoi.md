# FUTOI Reference

## Scope

FUTOI is Futures Open Interest by participant group. It is available for the futures market (`FO`) and is updated on a 5-minute cadence. Access is plan-dependent: local docs conflict between 15-minute delayed access and T-15-day free/starter availability, so do not promise a specific delay without checking DataShop current entitlement.

## DataFrame Calls

```python
import os
from moexalgo import session, Market, Ticker

session.TOKEN = os.environ["APIKEY"]

fo = Market("FO")
all_futoi = fo.futoi(date="2025-01-10")

contract = Ticker("SiH5")
si = contract.futoi(start="2025-01-10", end="2025-01-10")
```

`Market("FO").futoi(date=...)` returns all FUTOI rows for a date. `Ticker(...).futoi(start=..., end=...)` resolves the instrument to the underlying FUTOI identifier used by the endpoint.

## Direct Endpoints

- All instruments: `/iss/analyticalproducts/futoi/securities.json?date=YYYY-MM-DD`
- One instrument/underlying: `/iss/analyticalproducts/futoi/securities/{ticker}.json?from=YYYY-MM-DD&till=YYYY-MM-DD`

Use `https://apim.moex.com/iss/...` plus bearer token for authenticated access.

## Fields

| Field | Meaning |
| --- | --- |
| `sess_id` | Trading session id |
| `seqnum` | Technical sequence number |
| `tradedate` | Date |
| `tradetime` | Time of latest trade included in the snapshot |
| `ticker` | Short underlying/instrument code |
| `clgroup` | `FIZ` for individuals, `YUR` for legal entities |
| `pos` | Net position, long minus short |
| `pos_long` | Gross long open positions in contracts |
| `pos_short` | Gross short open positions in contracts; can be negative in raw data |
| `pos_long_num` | Count of participants with long positions |
| `pos_short_num` | Count of participants with short positions |

## Aggregation Caveats

- FUTOI aggregates active contract series by underlying/instrument. For example, rows for `Si` can combine positions across multiple active Si futures contracts.
- A trader's positions are netted within one contract first.
- A trader can count in both long and short participant counts if they are long one contract series and short another.
- Zero net positions are excluded from participant counts.
- Do not infer individual trader identities or exact single-contract FIZ/YUR splits from aggregate rows.

## Analysis Snippets

Compare FIZ/YUR net positions:

```python
df = fo.futoi(date="2025-01-10")
latest_time = df["tradetime"].max()
latest = df[df["tradetime"] == latest_time]
pivot = latest.pivot_table(index="ticker", columns="clgroup", values="pos", aggfunc="sum")
```

Participant concentration proxy:

```python
df = contract.futoi(start="2025-01-10", end="2025-01-10")
df["avg_long_per_participant"] = df["pos_long"] / df["pos_long_num"].replace(0, float("nan"))
df["avg_short_per_participant"] = df["pos_short"].abs() / df["pos_short_num"].replace(0, float("nan"))
```

Long/short imbalance by group:

```python
df["gross_total"] = df["pos_long"] + df["pos_short"].abs()
df["long_share"] = df["pos_long"] / df["gross_total"].replace(0, float("nan"))
```
