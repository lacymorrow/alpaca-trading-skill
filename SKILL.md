---
name: alpaca-trading
description: >-
  Trade stocks, ETFs, options, and crypto via Alpaca's REST API using curl.
  Manage orders, positions, watchlists, and portfolio history. Access real-time
  and historical market data including quotes, bars, snapshots, news, and
  screener (most active, top movers). Use when the user mentions "buy," "sell,"
  "trade," "market data," "stock price," "stock quote," "portfolio," "account
  balance," "positions," "orders," "alpaca," "market clock," "watchlist,"
  "top movers," "most active," or any stock/crypto trading task. No external
  CLI binary required — uses curl + jq directly.
---

# Alpaca Trading Skill

Trade and manage portfolios via Alpaca's REST API using `scripts/alpaca.sh`.

## Setup

### Required env vars

| Variable | Purpose |
|----------|---------|
| `APCA_API_KEY_ID` | Alpaca API key |
| `APCA_API_SECRET_KEY` | Alpaca API secret |

### Optional env vars

| Variable | Default | Purpose |
|----------|---------|---------|
| `APCA_API_BASE_URL` | `https://paper-api.alpaca.markets` | Trading endpoint. Set to `https://api.alpaca.markets` for live |
| `APCA_DATA_API_BASE_URL` | `https://data.alpaca.markets` | Market data endpoint |

**Paper trading is the default.** Always confirm with the user before switching to live.

### Helper script

Source `scripts/alpaca.sh` from this skill directory. Usage:

```bash
alpaca METHOD PATH [JSON_BODY]
# Market data:
ALPACA_DATA=1 alpaca METHOD PATH
```

## Quick Reference

### Account & portfolio

```bash
alpaca GET /v2/account                                          # balance, equity, buying power
alpaca GET '/v2/account/portfolio/history?period=1M&timeframe=1D'  # portfolio chart
alpaca GET /v2/clock                                            # market open/closed
```

### Orders

```bash
# Market buy (by qty or dollar amount)
alpaca POST /v2/orders '{"symbol":"AAPL","qty":"10","side":"buy","type":"market","time_in_force":"day"}'
alpaca POST /v2/orders '{"symbol":"AAPL","notional":"1000","side":"buy","type":"market","time_in_force":"day"}'

# Limit / stop / trailing stop / bracket — see references/api.md

# List / cancel
alpaca GET /v2/orders
alpaca DELETE /v2/orders/ORDER_ID
alpaca DELETE /v2/orders                                        # cancel ALL
```

### Positions

```bash
alpaca GET /v2/positions                                        # all open
alpaca GET /v2/positions/AAPL                                   # single
alpaca DELETE /v2/positions/AAPL                                # close
alpaca DELETE '/v2/positions?cancel_orders=true'                # close ALL
```

### Market data

```bash
ALPACA_DATA=1 alpaca GET /v2/stocks/AAPL/snapshot               # quote + trade + bar
ALPACA_DATA=1 alpaca GET '/v2/stocks/AAPL/bars?timeframe=1Day&start=2026-03-01&limit=30'
ALPACA_DATA=1 alpaca GET /v1beta1/screener/stocks/most-actives
ALPACA_DATA=1 alpaca GET '/v1beta1/screener/stocks/movers?top=10'
ALPACA_DATA=1 alpaca GET '/v1beta1/news?symbols=AAPL&limit=5'
```

### Crypto

```bash
ALPACA_DATA=1 alpaca GET '/v1beta3/crypto/us/latest/quotes?symbols=BTC/USD'
ALPACA_DATA=1 alpaca GET '/v1beta3/crypto/us/bars?symbols=BTC/USD&timeframe=1Day&limit=30'
```

## Full API reference

Read `references/api.md` for complete endpoint documentation including:
- All order types (limit, stop, stop-limit, trailing stop, bracket, OCO, OTO)
- Order field reference (side, type, time_in_force, order_class)
- Watchlist CRUD
- Options contracts
- Historical bars/trades/quotes with all query params
- Crypto endpoints
- Market calendar

## Safety Rules

1. **Default to paper trading.** Never set `APCA_API_BASE_URL` to live without explicit user confirmation.
2. **Show the order JSON before submitting.** Let the user confirm symbol, qty, side, and type.
3. **Check market clock** before placing orders — stocks only trade during market hours (9:30-16:00 ET) unless `extended_hours: true` on limit orders.
4. **Verify buying power** via `GET /v2/account` before large orders.
5. **Never provide financial advice.** Present data; let the user decide.
6. **Warn about irreversible actions** — closing all positions, canceling all orders.

## Troubleshooting

| Symptom | Fix |
|---------|-----|
| `HTTP 401` | Check `APCA_API_KEY_ID` / `APCA_API_SECRET_KEY` are set and valid |
| `HTTP 403` | Paper keys on live URL or vice versa; check `APCA_API_BASE_URL` |
| `HTTP 422` (insufficient qty) | Check buying power, fractional support, or symbol validity |
| `HTTP 429` | Rate limited — wait and retry; Alpaca allows 200 req/min |
| Order rejected outside hours | Add `"extended_hours":true` (limit orders only) or wait for market open |
| `jq: command not found` | Script falls back to `python3 -m json.tool`; install jq for better output |
