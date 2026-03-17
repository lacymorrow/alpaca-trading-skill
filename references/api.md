# Alpaca REST API Reference

All endpoints use `alpaca.sh` wrapper: `alpaca METHOD PATH [JSON_BODY]`.
For market data endpoints, prefix with `ALPACA_DATA=1`.

## Trading API (`/v2`)

### Account

```bash
# Get account info (balance, buying power, equity, status)
alpaca GET /v2/account

# Get account activity (trades, dividends, transfers)
alpaca GET /v2/account/activities
# Filter by type: FILL, DIV, TRANS, CSD, etc.
alpaca GET '/v2/account/activities?activity_types=FILL,DIV'

# Get/update account config
alpaca GET /v2/account/configurations
alpaca PATCH /v2/account/configurations '{"dtbp_check":"both","fractional_trading":true}'

# Portfolio history
alpaca GET '/v2/account/portfolio/history?period=1M&timeframe=1D'
# period: 1D, 1W, 1M, 3M, 6M, 1A, all  |  timeframe: 1Min, 5Min, 15Min, 1H, 1D
```

### Orders

```bash
# Create market order (by qty)
alpaca POST /v2/orders '{"symbol":"AAPL","qty":"10","side":"buy","type":"market","time_in_force":"day"}'

# Create market order (by notional/dollar amount)
alpaca POST /v2/orders '{"symbol":"AAPL","notional":"1000","side":"buy","type":"market","time_in_force":"day"}'

# Limit order
alpaca POST /v2/orders '{"symbol":"AAPL","qty":"10","side":"buy","type":"limit","limit_price":"185.00","time_in_force":"gtc"}'

# Stop order
alpaca POST /v2/orders '{"symbol":"AAPL","qty":"10","side":"sell","type":"stop","stop_price":"180.00","time_in_force":"day"}'

# Stop-limit order
alpaca POST /v2/orders '{"symbol":"AAPL","qty":"10","side":"sell","type":"stop_limit","stop_price":"180.00","limit_price":"179.00","time_in_force":"day"}'

# Trailing stop (percent)
alpaca POST /v2/orders '{"symbol":"AAPL","qty":"10","side":"sell","type":"trailing_stop","trail_percent":"5","time_in_force":"gtc"}'

# Trailing stop (dollar)
alpaca POST /v2/orders '{"symbol":"AAPL","qty":"10","side":"sell","type":"trailing_stop","trail_price":"10.00","time_in_force":"gtc"}'

# Bracket order (entry + take-profit + stop-loss)
alpaca POST /v2/orders '{
  "symbol":"AAPL","qty":"10","side":"buy","type":"market","time_in_force":"day",
  "order_class":"bracket",
  "take_profit":{"limit_price":"200.00"},
  "stop_loss":{"stop_price":"175.00","limit_price":"174.00"}
}'

# OTO (one-triggers-other)
alpaca POST /v2/orders '{
  "symbol":"AAPL","qty":"10","side":"buy","type":"limit","limit_price":"185.00","time_in_force":"day",
  "order_class":"oto",
  "stop_loss":{"stop_price":"175.00"}
}'

# OCO (one-cancels-other)
alpaca POST /v2/orders '{
  "symbol":"AAPL","qty":"10","side":"sell","type":"limit","limit_price":"200.00","time_in_force":"day",
  "order_class":"oco",
  "stop_loss":{"stop_price":"175.00"}
}'

# List orders (default: open)
alpaca GET /v2/orders
alpaca GET '/v2/orders?status=all&limit=50&direction=desc'
# status: open, closed, all

# Get order by ID
alpaca GET /v2/orders/ORDER_ID

# Replace (modify) order
alpaca PATCH /v2/orders/ORDER_ID '{"qty":"20","limit_price":"190.00"}'

# Cancel order
alpaca DELETE /v2/orders/ORDER_ID

# Cancel ALL open orders
alpaca DELETE /v2/orders
```

#### Order fields reference

| Field | Values |
|-------|--------|
| `side` | `buy`, `sell` |
| `type` | `market`, `limit`, `stop`, `stop_limit`, `trailing_stop` |
| `time_in_force` | `day`, `gtc`, `opg`, `cls`, `ioc`, `fok` |
| `order_class` | (empty for simple), `bracket`, `oco`, `oto` |
| `extended_hours` | `true` for extended hours (limit orders only) |

### Positions

```bash
# List all open positions
alpaca GET /v2/positions

# Get position for symbol
alpaca GET /v2/positions/AAPL

# Close a position (market sell all shares)
alpaca DELETE /v2/positions/AAPL
# Close partial
alpaca DELETE '/v2/positions/AAPL?qty=5'
# Close by percentage
alpaca DELETE '/v2/positions/AAPL?percentage=50'

# Close ALL positions
alpaca DELETE '/v2/positions?cancel_orders=true'
```

### Assets

```bash
# List tradeable assets
alpaca GET '/v2/assets?status=active&asset_class=us_equity'

# Get asset by symbol
alpaca GET /v2/assets/AAPL

# Options contracts
alpaca GET '/v2/options/contracts?underlying_symbols=AAPL&expiration_date_gte=2026-04-01'
alpaca GET /v2/options/contracts/CONTRACT_ID
```

### Market Clock & Calendar

```bash
# Market clock (open/closed, next open/close)
alpaca GET /v2/clock

# Trading calendar
alpaca GET '/v2/calendar?start=2026-03-01&end=2026-03-31'
```

### Watchlists

```bash
# List watchlists
alpaca GET /v2/watchlists

# Create watchlist
alpaca POST /v2/watchlists '{"name":"Tech","symbols":["AAPL","MSFT","GOOGL","NVDA"]}'

# Get watchlist
alpaca GET /v2/watchlists/WATCHLIST_ID

# Update watchlist
alpaca PUT /v2/watchlists/WATCHLIST_ID '{"name":"Tech Leaders","symbols":["AAPL","MSFT","GOOGL","NVDA","META"]}'

# Add symbol to watchlist
alpaca POST /v2/watchlists/WATCHLIST_ID '{"symbol":"AMZN"}'

# Remove symbol from watchlist
alpaca DELETE /v2/watchlists/WATCHLIST_ID/SYMBOL

# Delete watchlist
alpaca DELETE /v2/watchlists/WATCHLIST_ID
```

---

## Market Data API

Prefix all commands with `ALPACA_DATA=1`.

### Stock Data

```bash
# Latest quote
ALPACA_DATA=1 alpaca GET /v2/stocks/AAPL/quotes/latest

# Latest trade
ALPACA_DATA=1 alpaca GET /v2/stocks/AAPL/trades/latest

# Snapshot (quote + trade + bar + prev close)
ALPACA_DATA=1 alpaca GET /v2/stocks/AAPL/snapshot

# Multi-snapshot
ALPACA_DATA=1 alpaca GET '/v2/stocks/snapshots?symbols=AAPL,MSFT,GOOGL'

# Historical bars
ALPACA_DATA=1 alpaca GET '/v2/stocks/AAPL/bars?timeframe=1Day&start=2026-03-01&end=2026-03-15&limit=50'
# timeframe: 1Min, 5Min, 15Min, 30Min, 1Hour, 1Day, 1Week, 1Month

# Historical trades
ALPACA_DATA=1 alpaca GET '/v2/stocks/AAPL/trades?start=2026-03-15&limit=100'

# Historical quotes
ALPACA_DATA=1 alpaca GET '/v2/stocks/AAPL/quotes?start=2026-03-15&limit=100'
```

### Crypto Data

```bash
# Latest crypto quote
ALPACA_DATA=1 alpaca GET /v1beta3/crypto/us/latest/quotes?symbols=BTC/USD

# Latest crypto trade
ALPACA_DATA=1 alpaca GET /v1beta3/crypto/us/latest/trades?symbols=BTC/USD

# Crypto bars
ALPACA_DATA=1 alpaca GET '/v1beta3/crypto/us/bars?symbols=BTC/USD&timeframe=1Day&limit=30'

# Crypto snapshot
ALPACA_DATA=1 alpaca GET '/v1beta3/crypto/us/snapshots?symbols=BTC/USD,ETH/USD'
```

### News

```bash
# Latest news
ALPACA_DATA=1 alpaca GET '/v1beta1/news?limit=10'

# News for symbol
ALPACA_DATA=1 alpaca GET '/v1beta1/news?symbols=AAPL,TSLA&limit=10'
```

### Screener

```bash
# Most active stocks
ALPACA_DATA=1 alpaca GET /v1beta1/screener/stocks/most-actives

# Top movers (gainers/losers)
ALPACA_DATA=1 alpaca GET '/v1beta1/screener/stocks/movers?top=10'
```
