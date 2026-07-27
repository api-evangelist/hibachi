---
name: Withdraw funds from Hibachi
description: Check balance, read the deposit/withdraw limits, and submit a signed withdrawal.
api: openapi/hibachi-openapi.yml
operations: [getCapitalBalance, getAccountInfo, submitWithdraw, getCapitalHistory]
---

# Withdraw from Hibachi

Capital movement is irreversible and always requires a signed payload — treat as high-consequence.

## Prerequisites
- `Authorization` API key for the account.
- The account's signing key (ECDSA or HMAC).

## Steps
1. **Check balance.** Call `getCapitalBalance` (`GET /capital/balance?accountId=...`) for net equity.
2. **Check the withdrawable amount.** Call `getAccountInfo` (`GET /trade/account/info?accountId=...`)
   and read `maximalWithdraw` — the quantity you request must not exceed it.
3. **Build and sign.** Assemble `accountId`, `coin`, `withdrawAddress`, `network` (e.g. `arbitrum`),
   `quantity`, `maxFees`, and the `signature` (65 bytes over assetId + quantity + maxFees + ...).
4. **Submit.** Call `submitWithdraw` (`POST /capital/withdraw`).
5. **Confirm.** Call `getCapitalHistory` (`GET /capital/history?accountId=...`) and check the most
   recent withdraw `status`.

## Rules
- `quantity` must be ≤ `maximalWithdraw` or the request is rejected (`400`).
- The signature is mandatory; the API key alone cannot authorize a withdrawal.
- Transfers to another Hibachi account use `submitTransfer` (`POST /capital/transfer`) with a `nonce`.
