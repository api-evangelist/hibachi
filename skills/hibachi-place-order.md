---
name: Place a perpetual futures order on Hibachi
description: Look up a market, place a signed limit or market order, and confirm it landed.
api: openapi/hibachi-openapi.yml
operations: [getExchangeInfo, getPrices, placeOrder, getOrder, listPendingOrders]
---

# Place an order on Hibachi

Use this to submit a perpetual futures order on the Hibachi exchange.

## Prerequisites
- An `Authorization` API key for the account (header on every call below).
- The account's signing key: an ECDSA private key (trustless/wallet accounts) or HMAC secret
  (exchange-managed/email accounts). Order payloads must be signed.

## Steps
1. **Discover the market.** Call `getExchangeInfo` (`GET /market/exchange-info`, public) to get the
   tradable `futureContracts` and their `symbol` (e.g. `BTC/USDT-P`) and `contractId`.
2. **Check the price** (optional). Call `getPrices` (`GET /market/data/prices?symbol=BTC/USDT-P`)
   for `bidPrice`/`askPrice`/`markPrice`.
3. **Build and sign the order.** Assemble the payload: `accountId`, `symbol`, `nonce` (a unique,
   increasing timestamp), `side` (`BID`/`ASK`), `orderType` (`LIMIT`/`MARKET`), `quantity`,
   `price`, `maxFeesPercent`. Compute the `signature` over the defined byte layout. Optionally set
   a `clientId` so you can reference the order later. Quantities and prices are strings.
4. **Submit.** Call `placeOrder` (`POST /trade/order`) with the signed body. The response returns
   `orderId`.
5. **Confirm.** Call `getOrder` (`GET /trade/order?accountId=...&orderId=...`) or
   `listPendingOrders` (`GET /trade/orders?accountId=...`) to verify `status`.

## Rules
- The `nonce` must be unique per request; it is replay protection, not idempotency — a retried
  submit with a fresh nonce creates a new order.
- Public market endpoints need no API key; trading endpoints need both the API key and the signature.
- A `400` usually means an invalid signature or a parameter out of range; `401` means a bad API key.
