# Wormhole Bridge — Signature Verification Bypass PoC (2022)

> **Educational Purpose Only** — This PoC is created for security research and education
> purposes only. It runs on a local Solana validator, not mainnet.

**Category:** Access Control Failures
**Loss:** ~$326M (120,000 wETH)
**Chain:** Solana (bridge from Ethereum)
**Date:** February 2, 2022

## Overview

The Wormhole cross-chain bridge was exploited for 120,000 wETH (~$326M) when an attacker
bypassed guardian signature verification on Solana. The attacker used a deprecated syscall
(`verify_signatures`) that could be spoofed to craft a fake VAA (Verified Action Approval),
claiming 120,000 ETH had been deposited on Ethereum and minting the equivalent wETH on
Solana without any actual deposit. Jump Crypto covered the loss by depositing 120,000 ETH.

## How It Works

1. **VulnerableBridge** accepts transfer messages checking only signature count, not validity
2. Attacker calls `complete_transfer_vulnerable` claiming 2 signatures — bridge trusts the count
3. 120,000 wETH minted without any ETH locked on Ethereum
4. **FixedBridge** verifies each signer is an actual guardian via `remaining_accounts`

## Quick Start

```bash
git clone https://github.com/NomosLabs-Security/poc-wormhole-bridge-2022
cd poc-wormhole-bridge-2022
solana-keygen new --no-bip39-passphrase --silent --force
yarn install
anchor build --no-idl
anchor keys sync
anchor build --no-idl
anchor test --skip-build
```

## Prerequisites

- [Rust](https://rustup.rs/) (v1.79+)
- [Solana CLI](https://docs.solana.com/cli/install-solana-cli-tools) (v1.18+)
- [Anchor](https://www.anchor-lang.com/docs/installation) (v0.30+)
- [Node.js](https://nodejs.org/) (v18+)

## Program Structure

- `programs/wormhole-exploit/src/lib.rs` — Simplified Wormhole-like bridge with guardian verification
- `tests/exploit.ts` — Step-by-step exploit reproduction and mitigation test

## Key Takeaway

Cross-chain bridge security depends entirely on the integrity of the message verification
layer. A single bypass in signature validation allows unlimited minting on the destination
chain. Bridges are the highest-value targets in DeFi because they hold the backing assets
for wrapped tokens — compromising the verification layer means total collateral loss.

## Disclaimer

Educational purposes only. This is a simplified simulation demonstrating the vulnerability
pattern on a local Solana validator, not a fork replay.

## License

MIT — For educational use only.
