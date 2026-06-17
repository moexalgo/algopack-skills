---
name: moex-iss
description: MOEX ISS and ISS+ mechanics for raw REST endpoints, public iss.moex.com access, authenticated apim.moex.com access, output formats, iss.only, iss.json, start pagination, response blocks, discovery and history routes, calendars, boardgroups, and websocket/STOMP concepts.
---

# MOEX ISS

## Overview

Use this skill for generic MOEX ISS/ISS+ mechanics that are not specific to one ALGOPACK dataset. Prefer dataset-specific skills for SuperCandles, FUTOI, HI2, Mega Alerts, and market-data examples.

## Core Workflow

1. Choose host: `https://iss.moex.com/iss` for public ISS or `https://apim.moex.com/iss` for authenticated subscriber routes.
2. Choose format suffix: `.json`, `.csv`, `.xml`, or `.html`.
3. Use `iss.only` and column filters to keep payloads small.
4. Parse ISS blocks with `columns` and `data`.
5. Paginate with `start` until the target block returns no rows.

## Quick Start

```bash
curl -L "https://iss.moex.com/iss/engines/stock/markets/shares/boards/TQBR/securities.json?iss.only=marketdata&marketdata.columns=SECID,LAST,UPDATETIME"
```

Authenticated:

```bash
curl -L "https://apim.moex.com/iss/datashop/algopack/eq/tradestats/SBER.json?from=2025-01-01&till=2025-01-31&start=0" \
  -H "Authorization: Bearer ${APIKEY}"
```

## References

Read these as needed:

- `references/rest-iss.md` for REST patterns, response parsing, pagination, discovery, history, and query parameters.
- `references/calendars.md` for trading calendars, sessions, suspensions, and settlement-related endpoints.
- `references/issplus.md` for ISS+ websocket/STOMP concepts and local beta client shape.

## Boundary

Never expose real keys in URLs, code samples, logs, screenshots, or responses. Use environment variables or placeholders.
