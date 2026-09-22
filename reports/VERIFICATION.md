# LicenseStake mainnet verification notes

**Not an audit.** Source matching / explorer verification only. Do not treat this as a security review.

| Field | Value |
| --- | --- |
| Chain | Ethereum mainnet (chainId `1`) |
| Address | [`0x7f35eDa1cd5dC2AB936f8C50e2683004D1fceEc3`](https://etherscan.io/address/0x7f35eDa1cd5dC2AB936f8C50e2683004D1fceEc3) |
| Creation tx | [`0x9c769794d9fafd6be205734a3718b4d6cd012f1ab41fc27b504fd3c9682ab7ce`](https://etherscan.io/tx/0x9c769794d9fafd6be205734a3718b4d6cd012f1ab41fc27b504fd3c9682ab7ce) |
| Creator / owner / treasury (at deploy) | `0x84c378bB5010208089EE2d6732850e3948495912` |
| Token (SIMN) | `0x2e3f8d10818807fa607be3e2AE53863d8d8F4235` |

## Compiler settings matched to deployed bytecode

From on-chain CBOR metadata + local recompile + immutable linking:

| Setting | Value |
| --- | --- |
| Compiler | **Solidity 0.8.36** (`0.8.36+commit.8a079791`) |
| Optimizer | **enabled**, **runs = 200** |
| viaIR | **false** (not used) |
| EVM version | **cancun** (also matches with `prague` / default `osaka` for this source) |
| Framework | Custom `solc` wasm compile (`scripts/compile-contract.mjs`); **no Foundry / Hardhat** in tree |

Runtime bytecode (after linking immutable `token`) matches on-chain runtime body. Metadata IPFS CID differs on recompile (expected).

### Constructor args

```text
token            = 0x2e3f8d10818807fa607be3e2AE53863d8d8F4235
treasury         = 0x84c378bB5010208089EE2d6732850e3948495912
organizerPrice   = 2000000000000000000000   # 2000e18
botPrice         = 1000000000000000000000    # 1000e18
```

ABI-encoded (no `0x` prefix) — also in `reports/constructor-args.txt`:

```text
0000000000000000000000002e3f8d10818807fa607be3e2ae53863d8d8f423500000000000000000000000084c378bb5010208089ee2d6732850e394849591200000000000000000000000000000000000000000000006c6b935b8bbd40000000000000000000000000000000000000000000000000003635c9adc5dea00000
```

Standard-JSON input used for Sourcify: `reports/standard-json-input.json`.

## Verification status (2026-09-22)

| Explorer | Status | URL |
| --- | --- | --- |
| **Sourcify** | **Success** — full match (`creationMatch` + `runtimeMatch`) | https://repo.sourcify.dev/1/0x7f35eDa1cd5dC2AB936f8C50e2683004D1fceEc3 |
| **Routescan** | **Verified** (via Sourcify external relay) | https://routescan.io/address/0x7f35eDa1cd5dC2AB936f8C50e2683004D1fceEc3?chainid=1 |
| **Etherscan** | **Blocked** — Sourcify→Etherscan relay: *"Daily limit of 500 source code submissions reached"*; no `ETHERSCAN_API_KEY` in agent env | https://etherscan.io/address/0x7f35eDa1cd5dC2AB936f8C50e2683004D1fceEc3#code |
| Blockscout | Relay 429 rate limit; not relied on | — |

**ask CoS/Thomas before Confirm-gated explorer publish** (Etherscan UI Confirm / new API key usage beyond existing env).

No mainnet redeploy. No secrets invented or committed.

## How to finish Etherscan later

Repo has no Foundry/`forge` or Hardhat verify config. Prefer Sourcify replay or Etherscan Standard-JSON once a key is available.

### Option A — Etherscan API v2 (needs key)

```bash
# After CoS/Thomas Confirm; do NOT invent keys
export ETHERSCAN_API_KEY=...   # from approved secret store only

# Manual Standard-JSON verify (UI or API):
# - Compiler: v0.8.36+commit.8a079791
# - Optimization: Yes, 200 runs
# - viaIR: No
# - EVM: cancun
# - License: MIT
# - Constructor args: contents of reports/constructor-args.txt
# - Source: reports/standard-json-input.json (or contracts/LicenseStake.sol single-file)
```

If Foundry is installed locally (optional; not in this repo):

```bash
forge verify-contract \
  0x7f35eDa1cd5dC2AB936f8C50e2683004D1fceEc3 \
  contracts/LicenseStake.sol:LicenseStake \
  --chain-id 1 \
  --compiler-version 0.8.36 \
  --num-of-optimizations 200 \
  --constructor-args $(cat reports/constructor-args.txt) \
  --etherscan-api-key "$ETHERSCAN_API_KEY"
```

(`--via-ir` must **not** be set.)

### Option B — Re-trigger Sourcify → Etherscan relay

```bash
curl -sS -X POST \
  'https://sourcify.dev/server/v2/verify/1/0x7f35eDa1cd5dC2AB936f8C50e2683004D1fceEc3' \
  -H 'Content-Type: application/json' \
  --data-binary @reports/sourcify-verify-body.json
# then poll https://sourcify.dev/server/v2/verify/<verificationId>
```

Contract is already fully matched on Sourcify; a later relay may publish to Etherscan when the daily quota resets.
