---
name: algopack-access
description: ALGOPACK access, subscription, API key, authorization, entitlement, and troubleshooting guidance for DataShop setup, bearer tokens, moexalgo session.TOKEN, direct REST headers, free or delayed access, 401, 403, 429, rate limits, and product boundaries.
---

# ALGOPACK Access

## Overview

Use this skill to help users get authenticated ALGOPACK access and diagnose subscription, key, entitlement, and rate-limit failures without exposing secrets.

## Core Workflow

1. Confirm whether the user is using direct REST/curl or the Python library.
2. For direct REST, show `Authorization: Bearer ${APIKEY}` against `https://apim.moex.com/iss`.
3. For Python library workflows, set `session.TOKEN` from an environment variable.
4. Map the symptom to missing/expired key, no product entitlement, wrong host, market/date coverage, or throttling.
5. State product boundaries: ALGOPACK supplies market data and analytics, not order placement.

## Quick Start

Direct REST:

```bash
curl -L "https://apim.moex.com/iss/datashop/algopack/eq/obstats.json?date=2025-01-10" \
  -H "Authorization: Bearer ${APIKEY}"
```

Python library:

```python
import os
from moexalgo import session, Market

session.TOKEN = os.environ["APIKEY"]
eq = Market("EQ")
rows = eq.obstats(date="2025-01-10")
```

## References

Read `references/access-troubleshooting.md` for subscription setup, access levels, error diagnosis, rate limits, and escalation notes.

## Boundary

Do not ask users to paste real API keys. Do not include real keys in code, shell history, notebooks, logs, or responses.
