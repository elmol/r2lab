# Spec: S1a.4 — Attester Register

## Goal

Create the `attester` binary with a `register` subcommand that registers a device on-chain via Alloy.

## Story Reference

- **S1a** — Verify Device Data Origin (The Wire)
- Covers S1.3 AC-1 (happy path registration)

## Related ADRs

- ADR-0001: Monorepo flat structure
- ADR-0003: Alloy for Rust-EVM bindings

## What to Build

### New crate: `attester/`

Added to the workspace `Cargo.toml`:

```toml
[workspace]
members = ["device", "attester"]
```

**Dependencies:** `clap` (CLI), `alloy` (EVM interaction — provider, contract calls, types), `serde` + `serde_json` (for verify in next spec), `tokio` (async runtime).

No dependency on `common/`.

### ABI binding

```rust
alloy::sol!(
    #[sol(rpc)]
    HardTrustRegistry,
    "contracts/out/HardTrustRegistry.sol/HardTrustRegistry.json"
);
```

This reads the ABI from Foundry's build output. `forge build` must run before `cargo build`.

### `attester register` CLI

```
attester register --serial-hash <BYTES32_HEX> --address <ETH_ADDRESS> --rpc <URL> --private-key <HEX> --contract <ADDRESS>
```

### Behavior

- Creates a provider connected to the RPC URL
- Creates a wallet signer from the private key
- Calls `registerDevice(serialHash, deviceAddr)` on the contract
- Prints the transaction hash on success
- Lets Alloy errors propagate on failure

Note: the caller pre-computes the keccak256 hash of the serial string (e.g., `cast keccak "HARDCODED-001"`). The CLI takes the hash directly.

### Tests

- Integration test (requires Anvil): register succeeds with authorized attester
- Mark integration tests `#[ignore]` for unit test runs

## What NOT to Build

- Duplicate check handling
- Unauthorized caller handling
- Custom error messages
- Any verify logic (that's the next spec)

## Validation

```bash
anvil &
cd contracts && forge build && forge script script/Deploy.s.sol --rpc-url http://127.0.0.1:8545 --broadcast
SERIAL_HASH=$(cast keccak "HARDCODED-001")
cargo run --bin attester -- register \
  --serial-hash $SERIAL_HASH \
  --address 0x1234567890abcdef1234567890abcdef12345678 \
  --rpc http://127.0.0.1:8545 \
  --private-key 0x59c6995e998f97a5a0044966f0945389dc9e86dae88c7a8412f4603b6b78690d \
  --contract <deployed-address>
# Expected: prints tx hash
```

## Handoff Prompt

```
Create the attester binary with a register command for HardTrust (Slice 1a).

Read the spec at docs/specs/s1a.4-attester-register.spec.md first.

Create attester/ as a new workspace member. Uses Alloy sol! macro for ABI binding
from contracts/out/. The register subcommand submits a registerDevice tx on-chain.
forge build must run before cargo build (sol! macro reads the ABI).
Run against Anvil to verify it prints a tx hash.
```
