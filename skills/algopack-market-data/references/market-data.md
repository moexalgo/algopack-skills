# MOEXAlgo Market Data Reference

## Contents

- [Imports and Auth](#imports-and-auth)
- [Markets and Boards](#markets-and-boards)
- [Field Selection](#field-selection)
- [Market Methods](#market-methods)
- [Ticker Methods](#ticker-methods)
- [Edge Cases](#edge-cases)
- [Raw Endpoint Map](#raw-endpoint-map)
- [Important Fields](#important-fields)
- [DataFrame Snippets](#dataframe-snippets)

## Imports and Auth

```python
import os
from moexalgo import session, Market, Ticker

session.TOKEN = os.environ["APIKEY"]
```

All examples return pandas DataFrames by default. Use `native=True` only when the user explicitly wants iterators/dicts.

## Markets and Boards

| Alias | Engine/market | Default board | Main methods |
| --- | --- | --- | --- |
| `EQ`, `eq`, `shares`, `stocks`, `equity` | `stock/shares` | `TQBR` | `tickers`, `marketdata`, `trades`, `candles`, ALGOPACK methods |
| `FO`, `fo`, `futures`, `forts` | `futures/forts` | `RFUD` | `tickers`, `marketdata`, `trades`, `candles`, ALGOPACK methods, `futoi` |
| `FX`, `fx`, `currency`, `selt` | `currency/selt` | `CETS` | `tickers`, `marketdata`, ALGOPACK methods |
| `index` | `stock/index` | `SNDX` | basic market data |
| `bonds` | `stock/bonds` | `TQOB` | basic market data |
| `options` | `futures/options` | `ROPD` | basic market data |

Override a board only when required:

```python
eq_tqpi = Market("EQ", board="TQPI")
instrument = Ticker("SOME_TICKER", board="TQPI")
```

## Field Selection

`tickers` and `marketdata` accept positional field names:

```python
eq = Market("EQ")
small = eq.tickers("ticker", "shortname", "minstep")
all_fields = eq.tickers("*")
quotes = eq.marketdata("ticker", "last", "bid", "offer", "updatetime")
```

Field names are normalized to lower-case in DataFrames. `SECID` appears as `ticker`, and `BOARDID` appears as `board`.

Default share ticker fields include `shortname`, `lotsize`, `decimals`, `minstep`, `issuesize`, `isin`, `regnumber`, `listlevel`.

Default futures ticker fields include `sectype`, `assetcode`, `shortname`, `lotvolume`, `decimals`, `minstep`, `initialmargin`, `lasttradedate`.

Default FX ticker fields include `shortname`, `lotsize`, `decimals`, `minstep`, `secname`.

## Market Methods

```python
eq = Market("EQ")

tickers = eq.tickers("ticker", "shortname", "minstep")
marketdata = eq.marketdata("ticker", "last", "bid", "offer")
trades = eq.trades()
last_two_market_candles = eq.candles()
```

`Market.candles()` returns two recent 1-minute candles per instrument, calculated from current-day trades. It is not the same as historical `Ticker.candles(...)`.

## Ticker Methods

```python
sber = Ticker("SBER")

info = sber.info("ticker", "title", "market", "engine", "decimals")
trades = sber.trades()
latest_trade = sber.trades(latest=True)
orderbook = sber.orderbook()
candles = sber.candles(start="2025-01-01", end="2025-01-31", period="1h")
```

Common candle periods:

- Native ISS intervals: `"1min"`, `"10min"`, `"1h"`, `"1D"`, `"1W"`, `"1M"`, or numeric `1`, `10`, `60`, `24`, `7`, `31`.
- Library resampled intervals: `"5min"`, `"15min"`, `"20min"`, `"30min"`, `"2h"`, `"3h"`, `"6h"`, `"12h"`, `"5D"`, `"10D"`, `"2W"`, `"4W"`.

`Ticker.candles(...)` expects both `start` and `end` for historical ranges. If only one side is known, choose an explicit bounded range instead of relying on implicit defaults.

## Edge Cases

- Local notes have marked some indexes, including `MOEXIT`, `MOEXRE`, and `MCXSM`, as daily-only, but live ISS availability can differ by ticker/date. Check the raw candles endpoint for the requested interval and do not assume intraday candles exist for every index.
- Instruments can move between boards, and local notes about specific board moves can become stale. For instruments such as `UWGN`, inspect metadata for the relevant date range, pass `Ticker("UWGN", board="...")` explicitly when needed, and stitch historical data from multiple boards when the range spans a migration.
- For non-default boards or indexes, inspect raw ISS metadata such as `/iss/securities/{ticker}.json` or board/boardgroup endpoints before assuming the library default board.

## Raw Endpoint Map

These are useful for explaining what the library calls:

| Workflow | Direct endpoint pattern |
| --- | --- |
| EQ securities/marketdata | `/iss/engines/stock/markets/shares/boards/TQBR/securities.json` |
| FO securities/marketdata | `/iss/engines/futures/markets/forts/boards/RFUD/securities.json` |
| FX securities/marketdata | `/iss/engines/currency/markets/selt/boards/CETS/securities.json` |
| Ticker info | `/iss/securities/{ticker}.json` |
| Ticker candles | `/iss/engines/{engine}/markets/{market}/boards/{board}/securities/{ticker}/candles.json` |
| Ticker trades | `/iss/engines/{engine}/markets/{market}/boards/{board}/securities/{ticker}/trades.json` |
| Ticker orderbook | `/iss/engines/{engine}/markets/{market}/boards/{board}/securities/{ticker}/orderbook.json` |

## Important Fields

Marketdata:

- `bid`, `offer`: best bid/offer.
- `biddeptht`, `offerdeptht`: total visible depth.
- `open`, `high`, `low`, `last`, `waprice`: intraday price fields.
- `numtrades`, `voltoday`, `valtoday`: activity and turnover.
- `updatetime`, `systime`: update timestamps.

Candles:

- `open`, `high`, `low`, `close`: OHLC.
- `volume`: lots/contracts/units depending on market.
- `value`: turnover value.
- `begin`, `end`: candle interval.

Trades:

- `tradeno` or `recno`: sequence key.
- `tradetime`, `systime`: trade time and system time.
- `price`, `quantity`, `value`.
- `buysell`: `B` or `S` where available.

Orderbook:

- `buysell`: bid (`B`) or ask (`S`).
- `price`, `quantity`.
- `seqnum`, `updatetime`.

## DataFrame Snippets

```python
tickers = eq.tickers("ticker", "shortname", "minstep")
quotes = eq.marketdata("ticker", "last", "bid", "offer")
joined = tickers.merge(quotes, on="ticker", how="left")
```

```python
candles = sber.candles(start="2025-01-01", end="2025-01-31", period="1h")
candles["begin"] = candles["begin"].astype("datetime64[ns]")
daily_volume = candles.groupby(candles["begin"].dt.date)["volume"].sum()
```
