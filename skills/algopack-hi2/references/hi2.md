# Direct HI2 Reference

## Meaning and Cadence

HI2 is a Herfindahl-Hirschman style concentration index:

```text
HI2 = sum(s_i ** 2)
```

where `s_i` is a participant share in percent. Shares of 50, 30, and 20 produce `2500 + 900 + 400 = 3800`.

Local docs describe daily calculation near the end of the trading day for supported shares, futures, and currency instruments. If too few participants trade an instrument, HI2 may not be calculated for that date.

## Endpoints

Use `https://apim.moex.com/iss` with a bearer token.

```text
/iss/datashop/algopack/eq/hi2.json?date=YYYY-MM-DD
/iss/datashop/algopack/eq/hi2/{ticker}.json?from=YYYY-MM-DD&till=YYYY-MM-DD
/iss/datashop/algopack/fo/hi2.json?date=YYYY-MM-DD
/iss/datashop/algopack/fo/hi2/{ticker}.json?from=YYYY-MM-DD&till=YYYY-MM-DD
/iss/datashop/algopack/fx/hi2.json?date=YYYY-MM-DD
/iss/datashop/algopack/fx/hi2/{ticker}.json?from=YYYY-MM-DD&till=YYYY-MM-DD
```

Examples:

```bash
curl -L "https://apim.moex.com/iss/datashop/algopack/eq/hi2.json?date=2025-01-10" \
  -H "Authorization: Bearer ${APIKEY}"
```

```bash
curl -L "https://apim.moex.com/iss/datashop/algopack/eq/hi2/SBER.csv?from=2025-01-01&till=2025-01-31" \
  -H "Authorization: Bearer ${APIKEY}"
```

## Response Block

HI2 JSON uses the `data` block:

```javascript
const block = payload.data;
const rows = block.data.map((row) =>
  Object.fromEntries(block.columns.map((name, index) => [name.toLowerCase(), row[index]]))
);
```

Raw ISS uses `secid`; user-facing tables can label it `ticker`.

## Fields

| Field | Meaning |
| --- | --- |
| `tradedate` | Date |
| `tradetime` | Metric time |
| `secid` | Instrument id |
| `metric` | HI2 metric name |
| `value` | HHI value |
| `reference` | Additional context, often null |
| `systime` | Load/system time |

## Metric Names

Use actual returned metric values when possible. Common names:

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

| Value | Interpretation |
| --- | --- |
| `< 1500` | Lower concentration |
| `1500-2500` | Moderate concentration |
| `> 2500` | High concentration |

These are broad market-structure bands, not trading rules.

## Chart-Ready Recipes

Metric time series:

```text
x = tradedate
series = metric
y = value
filter = secid == requested ticker
```

Market heatmap:

```text
rows = secid
columns = metric
values = value
filter = tradedate == requested date
```

Band labels:

```text
band = value < 1500 ? "low" : value <= 2500 ? "moderate" : "high"
```
