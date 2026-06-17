# Direct FUTOI Reference

## Scope

FUTOI is futures open interest by participant group. Local docs describe 5-minute snapshots, history from 2020, and FIZ/YUR segmentation for the futures market. Access is plan-dependent; local docs conflict between 15-minute delayed access and T-15-day starter/free availability, so do not promise a specific delay window without checking current entitlement.

## Endpoints

Use `https://apim.moex.com/iss` with a bearer token for authenticated access.

```text
/iss/analyticalproducts/futoi/securities.json?date=YYYY-MM-DD
/iss/analyticalproducts/futoi/securities/{ticker}.json?from=YYYY-MM-DD&till=YYYY-MM-DD
```

Examples:

```bash
curl -L "https://apim.moex.com/iss/analyticalproducts/futoi/securities.json?date=2025-01-10" \
  -H "Authorization: Bearer ${APIKEY}"
```

```bash
curl -L "https://apim.moex.com/iss/analyticalproducts/futoi/securities/Si.json?from=2025-01-10&till=2025-01-10" \
  -H "Authorization: Bearer ${APIKEY}"
```

For chart files, request `.csv` instead of `.json`.

## Response Block

FUTOI JSON uses the `futoi` block:

```javascript
const block = payload.futoi;
const rows = block.data.map((row) =>
  Object.fromEntries(block.columns.map((name, index) => [name.toLowerCase(), row[index]]))
);
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

- FUTOI aggregates active contract series by underlying/instrument. A row for `Si` can combine positions across active Si contracts.
- Positions are netted within one trader and one contract before aggregation.
- One participant can count in both long and short participant counts if they are long one contract and short another.
- Zero net positions are excluded from participant counts.
- Rows do not identify individual traders and should not be used to infer individual behavior.

## Chart-Ready Recipes

FIZ/YUR net-position line chart:

```text
x = tradedate + " " + tradetime
series = clgroup
y = pos
```

Gross long/short chart:

```text
long_y = pos_long
short_y = abs(pos_short)
facet_or_color = clgroup
```

Participant-count chart:

```text
long_count = pos_long_num
short_count = pos_short_num
```

Average contracts per participant:

```text
avg_long = pos_long / pos_long_num
avg_short = abs(pos_short) / pos_short_num
```

Guard against division by zero and missing rows when plotting.
