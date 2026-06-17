# ALGOPACK Access and Troubleshooting

## Subscription and Key Setup

- Register and sign in at DataShop: `https://data.moex.com`.
- Subscribe to ALGOPACK from the product page: `https://data.moex.com/products/algopack`.
- Generate the API key in the personal account: `https://data.moex.com/personal-account`.
- Treat commercial terms, prices, key lifetime, and current entitlement as account-facing details that can change.
- Never paste real API keys into source files, notebooks, shell history, logs, screenshots, or chat replies.

Direct REST pattern:

```bash
curl -L "https://apim.moex.com/iss/datashop/algopack/eq/obstats.json?date=2025-01-10" \
  -H "Authorization: Bearer ${APIKEY}"
```

Python library pattern:

```bash
python -m pip install "moexalgo[dataframe]" python-dotenv
```

```python
import os
from dotenv import load_dotenv
from moexalgo import session, Market

load_dotenv()
session.TOKEN = os.environ["APIKEY"]

eq = Market("EQ")
data = eq.obstats(date="2025-01-10")
```

Set `APIKEY=...` in a local `.env` file and keep that file out of source control. Setting `session.TOKEN` switches the library base URL to `https://apim.moex.com/iss` and adds `Authorization: Bearer ...`.

## Access Levels

It is fine to tell users plainly that real-time or fully up-to-date data requires a valid bearer token and the relevant product entitlement. Public ISS data can be delayed, limited by fields, or unavailable for subscriber-only datasets.

Known local product guidance:

| Dataset | Not-authorized behavior |
| --- | --- |
| Securities, marketdata, candles, trades | Usually delayed and/or limited fields |
| OrderBook | Usually 403, subscription required |
| SuperCandles: `tradestats`, `orderstats`, `obstats` | Usually 403, subscription required |
| HI2 | Usually 403, subscription required |
| FUTOI | Plan-dependent; local notes conflict between 15-minute delayed access and T-15-day delayed/free availability |
| Mega Alerts | Usually 403, subscription required |

Do not promise a specific free FUTOI delay window. Direct users to DataShop or the personal account for authoritative current entitlement.

## Error Diagnosis

| Symptom | Likely cause | Action |
| --- | --- | --- |
| `401` | Missing, malformed, expired, or rejected bearer token | Check `APIKEY`, bearer header, key renewal, and account status |
| `403` on subscription datasets | No valid subscription, no token, wrong host, or endpoint outside plan | Use `apim.moex.com`, set token/header, verify subscription and endpoint coverage |
| `403` with "Too Many Requests" or sudden blocks | Rate or network-protection limit | Slow requests, add backoff, batch by date/ticker, avoid high concurrency |
| `429` | Rate limit | Retry with exponential backoff and reduce concurrency |
| Non-JSON response in library code | Often auth, entitlement, or route mismatch | Inspect status code and headers with a direct request |
| Empty data | No data for date, closed market, unsupported instrument/date, delayed publication, or no entitlement | Check calendar, try a known liquid ticker/date, inspect raw endpoint |

Local wiki notes mention possible blocking around very high request rates, with normal low-rate usage usually fine. Recommend conservative throttling instead of relying on upper limits.

## Product Boundaries

ALGOPACK provides:

- Historical data for research, hypotheses, and backtests.
- Online market data for algorithmic decision systems.
- Analytics such as SuperCandles, FUTOI, HI2, and Mega Alerts.

ALGOPACK does not send exchange orders. For buy, sell, place, cancel, or route-order tasks, direct the user to their broker API or another authorized trading interface.

## Support Escalation

For account-specific subscription checks, payment issues, or key generation failures, direct users to the DataShop personal account and support channels. Local docs list `algopack@moex.com`, `@moex_algopack`, and `@moex_algopack_news` for product/community questions.
