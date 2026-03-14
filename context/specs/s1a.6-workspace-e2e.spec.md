# Spec: S1a.6 — Workspace Config + E2E Validation

## Goal

Wire everything together: workspace configuration, justfile commands, and end-to-end validation that the full chain works.

## Story Reference

- **S1a** — Verify Device Data Origin (The Wire)
- This spec validates all S1a ACs end-to-end

## What to Build

### Root `Cargo.toml`

```toml
[workspace]
members = ["device", "attester"]
```

Note: `common/` is NOT a member. It does not exist in Slice 1a.

### Justfile updates

```just
# Build contracts
forge-build:
    cd contracts && forge build

# Build all Rust crates (requires contracts built first for ABI)
build: forge-build
    cargo build --workspace

# Run all tests
test: forge-build
    cd contracts && forge test
    cargo test --workspace
```

### End-to-end validation

The following terminal session must work from start to finish:

```bash
# 0. Start local chain
anvil &

# 1. Build and deploy
cd contracts && forge build && cd ..
cd contracts && forge script script/Deploy.s.sol --rpc-url http://127.0.0.1:8545 --broadcast && cd ..

# 2. Device init
cargo run --bin device -- init
# Expected: Serial: HARDCODED-001 / Address: 0x<hardcoded>

# 3. Register device on-chain
SERIAL_HASH=$(cast keccak "HARDCODED-001")
cargo run --bin attester -- register \
  --serial-hash $SERIAL_HASH \
  --address 0x1234567890abcdef1234567890abcdef12345678 \
  --rpc http://127.0.0.1:8545 \
  --private-key 0x59c6995e998f97a5a0044966f0945389dc9e86dae88c7a8412f4603b6b78690d \
  --contract 0x<deployed-contract-address>
# Expected: prints tx hash

# 4. Device emit
cargo run --bin device -- emit
# Expected: "Reading written to reading.json"

# 5. Verify registered device → VERIFIED
cargo run --bin attester -- verify \
  --file reading.json \
  --rpc http://127.0.0.1:8545 \
  --contract 0x<deployed-contract-address>
# Expected: VERIFIED

# 6. Verify unregistered device → UNVERIFIED
# Create reading_unregistered.json with a different address
cargo run --bin attester -- verify \
  --file reading_unregistered.json \
  --rpc http://127.0.0.1:8545 \
  --contract 0x<deployed-contract-address>
# Expected: UNVERIFIED
```

## What NOT to Build

- CI pipeline changes (existing CI runs `just test`)
- README updates for device/ or attester/
- Integration test automation (deferred to Slice 1c)

## Validation

- `just build` succeeds (forge build + cargo build)
- `just test` succeeds (forge test + cargo test)
- End-to-end session above completes: VERIFIED + UNVERIFIED

## Handoff Prompt

```
Wire together the workspace and validate end-to-end for HardTrust (Slice 1a).

Read the spec at docs/specs/s1a.6-workspace-e2e.spec.md first.

Update the root Cargo.toml workspace members and justfile commands.
Run the full end-to-end validation: anvil + deploy + init + register + emit + verify.
VERIFIED for registered device, UNVERIFIED for unregistered.
Branch: feat/s1a-the-wire
```
