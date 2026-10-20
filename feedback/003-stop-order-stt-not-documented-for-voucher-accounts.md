# Stop orders require STT; paper-USDso (voucher) accounts start with 0 STT — not documented

Severity: normal
Area: docs

## Steps to reproduce

1. Claim the perps paper-USDso voucher on Hideki (1,000 USDso credit via `claimPerpVoucher`).
2. Open a position (e.g. long BTC-PERP).
3. Attempt to arm a stop-loss bracket via `armBracket` or `placePerpStopOrder`.

```ts
// stops.ts comment (line 14-17):
// Every pending stop locks SOMI (0.15 on the live markets, a pair twice that).
// It funds the trigger's gas and comes back on cancel, so an account
// holding only collateral cannot arm one.
```

4. The transaction reverts because the account holds 0 STT (native gas token).

Live confirmation:
```
✓ Hideki is live, block: 27866313
STT (native gas) balance of a fresh voucher-funded address: 0 wei
Stop orders require 0.15 STT each (locked as trigger gas)
→ A freshly voucher-funded account CANNOT place stop orders
```

## Expected

The Perps Builder Bounty getting-started guide and the "Get test funds" section of
`app.testnet.dreamdex.io/docs/developers/perps-testnet` should state:

- Stop orders (including take-profit/stop-loss brackets) require **0.15 STT per leg**
  locked as trigger gas.
- A voucher-funded account receives paper USDso only and starts with 0 STT.
- To use stop orders, request STT gas separately (via the app's "Send it … for gas"
  button or by asking in Discord).
- Alternatively, use a `reduceOnly` limit order at the desired exit price instead of
  a stop order — this requires no STT.

## Actual

The developer docs and bounty guide mention the STT requirement only in a code comment
inside `stops.ts`. There is no mention in:
- The perps testnet developer guide
- The bounty description
- The "Get test funds" section
- The "Common pitfalls" list in the developer docs

A builder following the quickstart will open a position, attempt to add risk management
via a stop-loss, and receive a silent revert (`InsufficientNativeForStopPayment` or similar)
with no guidance on why or how to fix it.

## Environment

- Network: Somnia Tokyo testnet (chain 50383), block 27866313
- Wallet: fresh voucher-funded account (0 STT, 1000 paper USDso)
- Bot kit commit: 2579866
