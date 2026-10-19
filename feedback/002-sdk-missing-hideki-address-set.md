# @somnia-chain/markets-sdk has no SOMNIA_HIDEKI_TESTNET_ADDRESSES export

Severity: major
Area: SDK

## Steps to reproduce

1. Install `@somnia-chain/markets-sdk` (version bundled in bot-kit commit `2579866`).
2. Inspect available exports:

```js
const m = require('@somnia-chain/markets-sdk');
const addrKeys = Object.keys(m).filter(k => k.toLowerCase().includes('address'));
console.log(addrKeys);
// Output: [ 'SOMNIA_MAINNET_ADDRESSES', 'SOMNIA_TESTNET_ADDRESSES', 'ZERO_ADDRESS' ]
```

3. Open `packages/perp-core/src/exchange.ts`, line 56:

```ts
const base = {
  indexerUrl: config.indexerUrl,
  chain,
  wsRpcUrl: config.wsRpcUrl,
  addresses: SOMNIA_TESTNET_ADDRESSES,  // ← Shannon address set used for Hideki
};
```

The comment on lines 36–40 says:
> "The bundled testnet address set serves Hideki too: the singletons the SDK
> looks up sit at the same deterministic addresses there"

## Expected

Either:
- `SOMNIA_HIDEKI_TESTNET_ADDRESSES` is exported from the SDK so callers can be explicit, or
- The comment is confirmed accurate and the SDK docs state that `SOMNIA_TESTNET_ADDRESSES`
  is intentionally identical for both Shannon and Hideki.

## Actual

The SDK exports `SOMNIA_TESTNET_ADDRESSES` and `SOMNIA_MAINNET_ADDRESSES` only.
There is no `SOMNIA_HIDEKI_TESTNET_ADDRESSES`. The bot-kit comment claims the addresses
are the same, but this cannot be verified from the SDK alone: a developer integrating
directly (not through `perp-core`) has no way to know whether to use `SOMNIA_TESTNET_ADDRESSES`
for Hideki or needs a different set.

## Impact

Any developer building directly against the SDK for Hideki will use `SOMNIA_TESTNET_ADDRESSES`
by guesswork. If the singleton addresses ever diverge between Shannon and Hideki (e.g. after
a Hideki redeploy), their code will silently use the wrong addresses with no SDK-level error.

## Evidence

```
$ node -e "const m = require('./node_modules/@somnia-chain/markets-sdk/dist/index.js'); \
  console.log(Object.keys(m).filter(k => k.toLowerCase().includes('address')).join('\n'))"
SOMNIA_MAINNET_ADDRESSES
SOMNIA_TESTNET_ADDRESSES
ZERO_ADDRESS
```

## Environment

- SDK: `@somnia-chain/markets-sdk` (bundled in bot-kit `packages/perp-core/node_modules`)
- Network: Somnia Tokyo testnet (chain 50383) (chain 50383)
- Bot kit commit: 2579866
