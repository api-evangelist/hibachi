---
name: Read Hibachi market data
description: Pull public market data — contracts, prices, orderbook, klines, funding — with no auth.
api: openapi/hibachi-openapi.yml
operations: [getExchangeInfo, getPrices, getStats, getOrderbook, getKlines, getFundingRates, getMarketTrades]
---

# Read Hibachi market data

All endpoints here are public (`data-api.hibachi.xyz`) and require no API key.

## Steps
1. **List markets.** `getExchangeInfo` (`GET /market/exchange-info`) returns the `futureContracts`
   and their `symbol`s (e.g. `ETH/USDT-P`). Use these symbols in every call below.
2. **Prices.** `getPrices` (`GET /market/data/prices?symbol=ETH/USDT-P`) → bid/ask/mark/spot and
   funding-rate estimation.
3. **24h stats.** `getStats` (`GET /market/data/stats?symbol=...`) → high/low/volume.
4. **Orderbook.** `getOrderbook` (`GET /market/data/orderbook?symbol=...&depth=3&granularity=0.01`).
5. **Candlesticks.** `getKlines` (`GET /market/data/klines?symbol=...&interval=1h`). Intervals:
   `1min,5min,15min,1h,4h,1d,1w`; optional `fromMs`/`toMs`.
6. **Funding rates.** `getFundingRates` (`GET /market/data/funding-rates?symbol=...`).
7. **Recent trades.** `getMarketTrades` (`GET /market/data/trades?symbol=...`).

## Rules
- Prices/quantities are strings — parse as decimals, do not cast to float blindly.
- For real-time updates, subscribe to the `/ws/market` WebSocket stream instead of polling
  (see asyncapi/hibachi-ws-asyncapi.yml).
- Use `getUtcTimestamp` (`GET /exchange/utc-timestamp`) to sync your clock for signed calls.
