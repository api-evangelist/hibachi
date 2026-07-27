---
name: Cancel Hibachi orders
description: Cancel a single open order or batch-cancel all open orders for an account.
api: openapi/hibachi-openapi.yml
operations: [listPendingOrders, cancelOrder, cancelAllOrders]
---

# Cancel orders on Hibachi

## Prerequisites
- `Authorization` API key for the account.
- The account's signing key (ECDSA or HMAC) — cancels are signed.

## Steps
1. **List open orders.** Call `listPendingOrders` (`GET /trade/orders?accountId=...`) to get the
   `orderId` (or `clientId`) of each open order.
2. **Cancel one order.** Call `cancelOrder` (`DELETE /trade/order`) with `accountId`, exactly one
   of `orderId`/`clientId`, and a `signature`.
3. **Cancel everything.** Call `cancelAllOrders` (`DELETE /trade/orders`) with `accountId`, a unique
   `nonce`, and a `signature` computed over that nonce. The response is `{ "status": "success" }`.

## Rules
- Identify an order by exactly one of `orderId` or `clientId`.
- Batch cancel requires the `nonce` to be a unique valid timestamp used as the signature payload.
