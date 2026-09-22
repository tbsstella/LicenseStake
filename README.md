# LicenseStake

One Ethereum contract for Orbis creator licenses. Event organizers and bot operators share it.

| Tier | Role | Stake | What one position unlocks |
| --- | --- | --- | --- |
| 1 | Event organizer | 2000 SIMN | One concurrent live event |
| 2 | Bot operator | 1000 SIMN | One bot |

Entry fee is 5% at stake time (3% treasury, 1% referrer, 1% back to the staker). With no referrer the whole 5% goes to the treasury. `unstake` returns the remaining 95% principal and cannot be paused.

Staking token: [SIMN](https://etherscan.io/address/0x2e3f8d10818807fa607be3e2AE53863d8d8F4235) on Ethereum mainnet.

Deployed LicenseStake: [`0x7f35eDa1cd5dC2AB936f8C50e2683004D1fceEc3`](https://etherscan.io/address/0x7f35eDa1cd5dC2AB936f8C50e2683004D1fceEc3)

## Compile

```bash
npm install
npm run compile
```

Artifact: `contracts/build/LicenseStake.json`.

## Deploy

Set `DEPLOYER_KEY` in the environment. Do not commit it.

```bash
DEPLOYER_KEY=0x... node scripts/deploy-license.mjs
```

Optional: `TREASURY` (defaults to the deployer), `ETH_RPC`, `SIMN_ADDRESS`.

This repository has not been independently audited. Read `contracts/LicenseStake.sol` before sending funds.
