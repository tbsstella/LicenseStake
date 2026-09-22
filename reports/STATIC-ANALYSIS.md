# LicenseStake static analysis summary

**Not an audit.** Automated static analysis only (Slither + Aderyn). Do not claim the contract is audited based on this report.

| Tool | Version / notes | Full artifact |
| --- | --- | --- |
| Slither | `slither-analyzer` 0.11.6, `solc` 0.8.36 | `reports/slither-raw.json`, `reports/slither-console.txt` |
| Aderyn | Cyfrin aderyn v0.6.8 | `reports/aderyn-report.md`, `reports/aderyn-console.txt` |

Target: `contracts/LicenseStake.sol` (self-contained; no external imports).

## High findings

**None** (Slither High: 0 · Aderyn High: 0).

## Medium findings

**None** (Slither Medium: 0 · Aderyn has no Medium bucket in this run; High: 0, Low: 9).

## Low / Informational (notable)

### Slither

| Severity | Detector | Notes / false-positive call |
| --- | --- | --- |
| Low | `missing-zero-check` on `transferOwnership(to)` | Two-step ownership: `pendingOwner = to` then `acceptOwnership()`. Setting `to = address(0)` only clears pending if accepted by nobody useful; intentional pattern. Low risk. |
| Informational | `low-level-calls` in `_safeTransfer` / `_safeTransferFrom` | **Likely FP / by design** — needed for ERC-20s that omit bool return (commented in source for SIMN-style tokens). Return data is checked. |
| Informational | `unindexed-event-address` on `TreasurySet` | Style only; no security impact. |

### Aderyn (all Low)

| ID | Title | Notes |
| --- | --- | --- |
| L-1 | Centralization Risk | Expected: owner can pause stakes, set prices/fees/treasury, `stakeFor`. Production note already says transfer ownership to multisig. |
| L-2 | Large Numeric Literal | Style (`10_000` vs scientific). |
| L-3 | Literal Instead of Constant | Style. |
| L-4 | Local Variable Shadows State Variable | Naming hygiene. |
| L-5 | `nonReentrant` not first modifier | On `stakeFor`, `onlyOwner` precedes `nonReentrant`. Low practical risk here. |
| L-6 | PUSH0 Opcode | Shanghai+; mainnet supports PUSH0. |
| L-7 | Address State Variable Set Without Checks | Overlaps Slither ownership zero-check. |
| L-8 | Unsafe ERC20 Operation | Same intentional low-level ERC-20 handling as Slither. |
| L-9 | Unspecific Solidity Pragma | `^0.8.24` while deploy used 0.8.36 — pin noted in verification docs. |

## Explicit disclaimer

This output is **not** a substitute for a professional security audit. Soft PRODUCTION residual work: explorer source verification + static scan artifacts only. No stake-expansion marketing changes. No mainnet redeploy.
