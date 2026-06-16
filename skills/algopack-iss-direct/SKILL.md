---
name: algopack-iss-direct
description: Direct MOEX ISS, ISS+, and ALGOPACK REST usage for raw endpoints outside dataframe-first moexalgo workflows. Use when a user asks for raw REST or curl/requests examples, calendars, unsupported endpoints, ISS query parameters, pagination with start, iss.only/iss.json handling, .json/.csv/.xml/.html output formats, history endpoints, boardgroups/securities discovery, direct bearer-token calls, or websocket/STOMP ISS+ concepts.
---

# ALGOPACK ISS Direct

## Overview

Use this skill when the user needs raw ISS/ISS+ access instead of high-level `moexalgo` DataFrames. Prefer `moexalgo` for supported `Market` and `Ticker` workflows; use direct ISS for calendars, unsupported endpoints, exact REST payloads, or integration in non-Python stacks.

## REST Pattern

```python
import os
import requests

url = "https://apim.moex.com/iss/datashop/algopack/eq/tradestats/SBER.json"
headers = {"Authorization": f"Bearer {os.environ['APIKEY']}"}
params = {"from": "2025-01-01", "till": "2025-01-31", "start": 0}

resp = requests.get(url, headers=headers, params=params, timeout=30)
resp.raise_for_status()
payload = resp.json()
```

Use `https://apim.moex.com/iss/...` with a bearer token for subscriber ALGOPACK access. Use `https://iss.moex.com/iss/...` for public ISS access where authentication is not needed.

## Pagination Pattern

Use the `start` query parameter and repeat until the returned data block has no rows. Do not assume a single request returns a full history.

## References

Read these as needed:

- `references/rest-iss.md` for REST endpoint maps, requests/curl examples, response parsing, and pagination.
- `references/calendars.md` for trading-calendar endpoints and fields.
- `references/issplus.md` for ISS+ websocket/STOMP concepts and the local `moexalgo.beta.issplus` shape.

## Boundary

Do not expose real keys in curl, logs, screenshots, or examples. Use placeholders or environment variables.
