---
name: algopack-product-access
description: ALGOPACK product access, subscription, API key, authorization, and troubleshooting guidance. Use when a user asks how to subscribe to ALGOPACK, obtain or configure an APIKEY, set moexalgo session.TOKEN, understand free vs paid access, diagnose 401/403/429 responses, confirm product boundaries, or explain which ALGOPACK services require a subscription.
---

# ALGOPACK Product Access

## Overview

Use this skill to help users get authenticated access to ALGOPACK data and to diagnose access failures without exposing API keys. Prefer `moexalgo` DataFrame examples for library usage, and direct bearer-token examples only when the user is working with raw REST.

## Core Workflow

1. Confirm whether the user is using the `moexalgo` Python library or direct ISS/API requests.
2. For `moexalgo`, show environment-variable setup and assign `session.TOKEN`.
3. For direct REST, show `Authorization: Bearer <APIKEY>` headers without embedding real keys.
4. Map the symptom to access level: delayed/free data, subscription-required 403, expired or missing key, or rate limiting.
5. State product boundaries: ALGOPACK provides market data and analytics, not order placement.

## DataFrame-First Auth Pattern

Use this default pattern:

```python
import os
from moexalgo import session, Market

session.TOKEN = os.environ["APIKEY"]

eq = Market("EQ")
tickers = eq.tickers("ticker", "shortname", "minstep")
```

Do not hard-code user keys in examples. Do not copy local keys into responses, code samples, logs, or skill files.

## Troubleshooting

Use `references/access-troubleshooting.md` for:

- Subscription and API key setup flow.
- Free, paid, and delayed access behavior.
- 401, 403, and 429 diagnosis.
- Which datasets are subscription-only.
- REST/curl authentication examples.

## Boundaries

Never describe ALGOPACK as a trading/order-placement API. If the user wants to send orders, explain that ALGOPACK supplies data and analytics only; order placement must go through the user's broker or another authorized trading API.
