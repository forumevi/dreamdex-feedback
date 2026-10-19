# packages/core does not include Hideki — NETWORK=testnet points to Shannon

Severity: major
Area: SDK

## Steps to reproduce

1. Clone `somnia-chain/dreamdex-bot-kit` (commit `2579866`).
2. Run any spot or non-perp strategy with `NETWORK=testnet`.
3. Inspect the resolved config:

```ts
// packages/core/src/config/networks.ts
export const NETWORKS: Record<NetworkName, NetworkConfig> = {
  mainnet: { chainId: 5031, rpcUrl: "https://api.infra.mainnet.somnia.network", ... },
  testnet: { chainId: 50312, rpcUrl: "https://dream-rpc.somnia.network", ... },
  // Somnia Tokyo testnet (chain 50383) is absent
};
export type NetworkName = "mainnet" | "testnet";
```

Live probe output:
```
=== packages/core getNetwork() with NETWORK=testnet ===
  resolved chainId: 50312
  resolved restApi: https://stg.api.dreamdex.io/v0
  Hideki chainId is 50383, Shannon is 50312
  BUG: setting NETWORK=testnet points to Shannon (50312), not Hideki
```

## Expected

`packages/core` should expose `hideki` as a first-class network name (chain ID 50383,
RPC `https://api.hideki2.infra.testnet.somnia.network`, REST `https://app.testnet.dreamdex.io/api/v0`)
so that spot and non-perp strategies can target the same testnet as the Perps Builder Bounty.

## Actual

`NETWORK=testnet` resolves to Shannon (50312). There is no way to set Hideki in
`packages/core` without overriding all three env vars (`RPC_URL`, `REST_API_URL`, `WS_URL`)
and also hard-coding the chain ID separately, with no guidance in the docs or the `.env.example`.

## Impact

A builder following the quickstart who wants to place spot orders alongside their perp
strategy on Hideki has no supported path. `packages/perp-core` handles Hideki correctly
via its own `config.ts` with `NETWORK=hideki`; `packages/core` does not.

## Environment

- SDK: `@somnia-chain/markets-sdk` bundled in `packages/perp-core/node_modules`
- Network: Somnia Tokyo testnet (chain 50383) (chain 50383)
- Wallet: n/a (read-only probe)
- Bot kit commit: 2579866
