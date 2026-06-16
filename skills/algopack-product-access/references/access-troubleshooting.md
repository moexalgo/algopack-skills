# ALGOPACK Access and Troubleshooting

## Subscription and Key Setup

- Register and sign in at DataShop: `https://data.moex.com`.
- Subscribe to ALGOPACK from the product page: `https://data.moex.com/products/algopack`.
- Generate the API key in the personal account: `https://data.moex.com/personal-account`.
- Treat commercial terms, price, and key lifetime as account-facing details that can change; direct users to the personal account for current status.
- Never paste real API keys into source files, notebooks, shell history, logs, or chat replies.

`moexalgo` pattern:

```python
import os
from moexalgo import session, Market

session.TOKEN = os.environ["APIKEY"]

eq = Market("EQ")
tickers = eq.tickers("ticker", "shortname", "minstep")
```

Direct REST pattern:

```python
import os
import requests

url = "https://apim.moex.com/iss/datashop/algopack/eq/obstats.json"
headers = {"Authorization": f"Bearer {os.environ['APIKEY']}"}
params = {"date": "2025-01-10"}

response = requests.get(url, headers=headers, params=params, timeout=30)
response.raise_for_status()
payload = response.json()
```

curl pattern:

```bash
curl -L "https://apim.moex.com/iss/datashop/algopack/eq/obstats.json?date=2025-01-10" \
  -H "Authorization: Bearer ${APIKEY}"
```

## Access Levels

Known local product guidance:

| Dataset | Not authorized behavior |
| --- | --- |
| Securities, marketdata, candles, trades | Usually delayed and/or limited fields |
| OrderBook | Usually 403, subscription required |
| SuperCandles: `tradestats`, `orderstats`, `obstats` | Usually 403, subscription required |
| HI2 | Usually 403, subscription required |
| FUTOI | Plan-dependent; local notes conflict between 15-minute delayed access and T-15-day delayed/free availability |
| Mega Alerts | Usually 403, subscription required |

Do not promise a specific free FUTOI delay window. Local wiki notes mention both 15-minute delayed market data and starter-plan FUTOI availability around T-15 days. Treat current FUTOI entitlement as plan-dependent and direct users to DataShop or the personal account for authoritative status.

For authenticated `moexalgo`, setting `session.TOKEN` switches the base URL to `https://apim.moex.com/iss` and adds `Authorization: Bearer ...`.

## Error Diagnosis

| Symptom | Likely cause | Action |
| --- | --- | --- |
| `401` | Missing, malformed, expired, or rejected bearer token at an auth gateway | Check `APIKEY` env var, bearer header, key renewal, and account subscription |
| `403` on subscription datasets | No valid subscription, no token, wrong base host, or endpoint not included in plan | Use `session.TOKEN`, verify subscription in DataShop, check endpoint coverage |
| `403` with "Too Many Requests" or sudden blocks | Rate or network-protection limit | Slow requests, add backoff, batch by date/ticker, use pagination |
| `429` | Rate limit | Add retry with backoff and reduce concurrency |
| Non-JSON response in `moexalgo` | The library raises/normalizes this as an access-like error | Inspect status and headers; usually auth or entitlement |
| Empty DataFrame | No data for date, market closed, unsupported instrument/date, or delayed publication | Check trading calendar, try a known liquid ticker/date, inspect raw endpoint |

Local wiki guidance mentions very high request-rate tolerance but possible 403/blocking around excessive request rates. In normal applications, use conservative throttling and retries instead of relying on upper limits.

## Product Boundaries

ALGOPACK provides market data and analytics:

- Historical data for research, hypotheses, and backtests.
- Online market data for algorithmic decision systems.
- Calculated metrics: SuperCandles, FUTOI, HI2, Mega Alerts.

ALGOPACK does not send exchange orders. If a user asks to buy, sell, place, cancel, or route orders, redirect them to their broker's trading API or another authorized trading interface.

## Support Escalation

For account-specific subscription checks, payment issues, or key generation failures, direct the user to DataShop personal account support. For product/community questions, the local docs list `algopack@moex.com`, `@moex_algopack`, and `@moex_algopack_news`.
