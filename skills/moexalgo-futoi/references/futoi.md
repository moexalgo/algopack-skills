# MOEXAlgo FUTOI Reference

## Imports and Auth

```python
import os
from moexalgo import session, Market, Ticker

session.TOKEN = os.environ["APIKEY"]
```

FUTOI access is plan-dependent. Local docs conflict between 15-minute delayed and T-15-day starter/free availability, so do not promise a specific delay window without checking current entitlement.

## Method Signatures

```text
Market("FO").futoi(date=None, native=False)
Ticker(...).futoi(start=None, end=None, native=False)
```

Pass explicit dates. In the current repo code, omitting `date` on market methods or `start`/`end` on ticker methods reaches `prepare_from_till_dates(...)` and raises instead of silently using today.

FUTOI is implemented only for the futures market. `Ticker(...).futoi(...)` resolves the ticker to the FUTOI identifier used by the endpoint. Perpetual futures such as `USDRUBF`, `EURRUBF`, `CNYRUBF`, `IMOEXF`, `GLDRUBF`, `SBERF`, and `GAZPF` are handled specially by the library.

## Examples

All futures for one date:

```python
fo = Market("FO")
df = fo.futoi(date="2025-01-10")
```

One instrument range:

```python
si = Ticker("SiH5")
df = si.futoi(start="2025-01-10", end="2025-01-10")
```

Use `native=True` for dictionaries:

```python
rows = list(fo.futoi(date="2025-01-10", native=True))
```

## Fields

| Field | Meaning |
| --- | --- |
| `sess_id` | Trading session id |
| `seqnum` | Technical sequence number |
| `tradedate` | Date |
| `trade_session_date` | Trading-session date when present |
| `tradetime` | Snapshot time |
| `ticker` | Underlying or instrument code |
| `clgroup` | `FIZ` for individuals, `YUR` for legal entities |
| `pos` | Net position, long minus short |
| `pos_long` | Gross long open positions in contracts |
| `pos_short` | Gross short open positions in contracts; raw values can be negative |
| `pos_long_num` | Count of participants with long positions |
| `pos_short_num` | Count of participants with short positions |
| `systime` | Load/system time when present |

## Aggregation Caveats

- FUTOI aggregates active contract series by underlying/instrument.
- A participant's positions are netted within one contract first.
- A participant can count in both long and short groups if they are long one contract and short another.
- Zero net positions are excluded from participant counts.
- Do not infer individual identities or exact single-contract FIZ/YUR splits from aggregate rows.

## DataFrame Analysis

Latest FIZ/YUR net position by ticker:

```python
df = Market("FO").futoi(date="2025-01-10")
latest_time = df["tradetime"].max()
latest = df[df["tradetime"] == latest_time]
pivot = latest.pivot_table(index="ticker", columns="clgroup", values="pos", aggfunc="sum")
```

Gross long/short and participant counts:

```python
df = Ticker("SiH5").futoi(start="2025-01-10", end="2025-01-10")
df["gross_total"] = df["pos_long"] + df["pos_short"].abs()
df["long_share"] = df["pos_long"] / df["gross_total"].replace(0, float("nan"))
```

Average contracts per participant:

```python
df["avg_long_per_participant"] = df["pos_long"] / df["pos_long_num"].replace(0, float("nan"))
df["avg_short_per_participant"] = df["pos_short"].abs() / df["pos_short_num"].replace(0, float("nan"))
```

Time series for charting:

```python
df["datetime"] = df["tradedate"].astype(str) + " " + df["tradetime"].astype(str)
chart = df[["datetime", "ticker", "clgroup", "pos", "pos_long", "pos_short", "pos_long_num", "pos_short_num"]]
```
