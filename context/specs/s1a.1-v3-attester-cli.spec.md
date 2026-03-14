# Spec: S1a.1-V3 — Attester CLI with `register` Command

## Parent Story

S1a.1 — Register a Device and Confirm It On-Chain

## Goal

A Rust binary in `attester/` with a single `register` subcommand that submits a device registration transaction on-chain.

## What to Build

### `attester/` crate

- Add `attester` as a workspace member in root `Cargo.toml`
- Dependencies: `clap` (with `derive` feature), `alloy` (provider, signer, contract, sol-types), `tokio`
- **No `common/` crate.** ABI binding via `sol!` macro directly in `attester/`:
  ```rust
  alloy::sol!(
      #[sol(rpc)]
      HardTrustRegistry,
      "../contracts/out/HardTrustRegistry.sol/HardTrustRegistry.json"
  );
  ```
- `forge build` must run before `cargo build -p attester`

### `attester register` subcommand

```
attester register --serial <SERIAL> --device-address <ADDRESS> --contract <CONTRACT_ADDRESS>
```

Behavior:
1. Connect to `http://127.0.0.1:8545` (hardcoded RPC)
2. Use Anvil account #1 private key as signer (hardcoded: `0x59c6995e998f97a5a0044966f0945389dc9e86dae88c7a8412f4603b6b78690d`)
3. Compute `keccak256(serial)` using `alloy::primitives::keccak256`
4. Call `registerDevice(serialHash, deviceAddress)` on the contract
5. Print: `tx: 0x...`

### Test

- Integration test requiring Anvil + deployed contract, marked `#[ignore]`

## What NOT to Build

- No configurable RPC URL or private key (hardcoded for The Wire)
- No `verify` or `query` subcommand (that's S1a.2)
- No `common/` crate
- No error handling beyond `.expect()` with descriptive messages

## Validation

```bash
# Prerequisites: Anvil running, contract deployed (from V1)
cd contracts && forge build && cd ..
cargo build -p attester

cargo run --bin attester -- register \
  --serial HARDCODED-001 \
  --device-address 0x1234567890abcdef1234567890abcdef12345678 \
  --contract <CONTRACT_ADDRESS>
# Expected: prints "tx: 0x..."

# Confirm on-chain
cast call <CONTRACT_ADDRESS> "isAttester(address)" 0x70997970C51812dc3A010C7d01b50e0d17dc79C8 --rpc-url http://127.0.0.1:8545
# Expected: true
```

## Handoff Prompt

```
Create the attester CLI crate for HardTrust.

Read the spec at docs/specs/s1a.1-v3-attester-cli.spec.md first.
This crate uses Alloy sol! macro to read the ABI directly from contracts/out/.
No common/ crate. Contracts must be built first (forge build).

After implementation, start Anvil, deploy the contract, and run the register command.
```
