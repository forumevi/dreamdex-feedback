# dreamDEX Perps Builder Bounty — Feedback

Builder: forumevi
Verified at: Somnia Tokyo testnet (chain 50383) (chain 50383), block 27866313, bot-kit commit 2579866

## Findings

| # | File | Severity | Area | Summary |
|---|------|----------|------|---------|
| 001 | [feedback/001-core-missing-hideki-network.md](feedback/001-core-missing-hideki-network.md) | major | SDK | `packages/core` has no Hideki network — `NETWORK=testnet` points to Shannon (50312) |
| 002 | [feedback/002-sdk-missing-hideki-address-set.md](feedback/002-sdk-missing-hideki-address-set.md) | major | SDK | `@somnia-chain/markets-sdk` exports no `SOMNIA_HIDEKI_TESTNET_ADDRESSES` |
| 003 | [feedback/003-stop-order-stt-not-documented-for-voucher-accounts.md](feedback/003-stop-order-stt-not-documented-for-voucher-accounts.md) | normal | docs | Stop orders require 0.15 STT; paper-USDso accounts start with 0 STT — not documented |

## Verification method

Each finding was confirmed by a live probe against Somnia Tokyo testnet (chain 50383) RPC
(`https://api.hideki2.infra.testnet.somnia.network`) and/or by reading the SDK source
in the bot-kit repository. Probe scripts are included in the repository root:
- `probe-bug1.mjs` — BUG-1 network resolution
- `probe-bug2.mjs` / `probe-priceable.mjs` — BUG-2 address set + isPriceable check
- `probe-bug5.mjs` — BUG-5 STT balance

## What was checked and found clean

- Funding rate normalization (`fundingRate8h`, `annualizedFundingRate`, `fundingRatePerInterval`):
  all helpers take `fundingWindowSec` and `fundingIntervalSec` as runtime arguments, no hardcoded denominator.
- `isPriceable()` on BTC PerpPool: stable `true` across 5 reads (2 s apart) at time of testing.
  Flapping is documented as intermittent; no new finding.
