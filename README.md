# Alpaca Trading Skill

An [OpenClaw](https://github.com/openclaw/openclaw) agent skill for trading stocks, ETFs, options, and crypto via [Alpaca's](https://alpaca.markets) REST API.

## What it does

- **Trade**: Buy/sell stocks, ETFs, options, and crypto with all order types (market, limit, stop, trailing stop, bracket, OCO, OTO)
- **Portfolio**: View positions, account balance, buying power, portfolio history
- **Market Data**: Real-time quotes, bars, snapshots, news, screener (most active, top movers)
- **Manage**: Orders, watchlists, account configuration

## Requirements

- `curl` (standard on macOS/Linux)
- `jq` (optional, falls back to `python3 -m json.tool`)
- Alpaca API keys ([sign up free](https://app.alpaca.markets/signup))

No external CLI binaries or SDKs required.

## Setup

Set your Alpaca API credentials as environment variables:

```bash
export APCA_API_KEY_ID="your-key-id"
export APCA_API_SECRET_KEY="your-secret-key"
```

Paper trading is the default. For live trading:

```bash
export APCA_API_BASE_URL="https://api.alpaca.markets"
```

## Install

```bash
clawhub install lacymorrow/alpaca-trading-skill
```

Or clone directly:

```bash
git clone https://github.com/lacymorrow/alpaca-trading-skill.git ~/.openclaw/skills/alpaca-trading
```

## Structure

```
├── SKILL.md              # Main skill file (loaded by OpenClaw)
├── scripts/
│   └── alpaca.sh         # Thin curl wrapper for Alpaca REST API
└── references/
    └── api.md            # Complete API endpoint reference
```

## License

MIT
