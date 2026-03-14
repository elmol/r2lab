# Spec: S1a.5 — Attester Verify

## Goal

Add a `verify` subcommand to the `attester` binary that checks whether a data reading comes from a registered device.

## Story Reference

- **S1a** — Verify Device Data Origin (The Wire)
- Covers S1.6 AC-1 (VERIFIED) + AC-2 (UNVERIFIED)

## Related ADRs

- ADR-0003: Alloy for Rust-EVM bindings
- ADR-0004: Single smart contract for registry + attestation

## What to Build

### `attester verify` CLI

```
attester verify --file <PATH> --rpc <URL> --contract <ADDRESS>
```

### Behavior

- Reads and parses the JSON reading file
- Extracts the `serial` field from JSON
- Computes `keccak256(serial)` using `alloy::primitives::keccak256` to get the serial hash
- Calls `getDevice(serialHash)` on the contract
- If `deviceAddr != address(0)` AND matches the address from the JSON: prints **VERIFIED**
- If `deviceAddr == address(0)` or does not match: prints **UNVERIFIED**
- No signature verification (deferred to Slice 1b)

### Tests

- Integration test: VERIFIED for a registered device (requires deploy + register first)
- Integration test: UNVERIFIED for an unregistered device
- Mark integration tests `#[ignore]` for unit test runs

## What NOT to Build

- Signature verification (ECDSA recovery)
- INVALID result (tampered data detection)
- Custom error handling
- Multiple readings support

## Validation

```bash
# After deploy + register + emit:
cargo run --bin attester -- verify \
  --file reading.json \
  --rpc http://127.0.0.1:8545 \
  --contract <deployed-address>
# Expected: VERIFIED

# With a modified reading (different address):
cargo run --bin attester -- verify \
  --file reading_unregistered.json \
  --rpc http://127.0.0.1:8545 \
  --contract <deployed-address>
# Expected: UNVERIFIED
```

## Handoff Prompt

```
Add the verify subcommand to the attester binary for HardTrust (Slice 1a).

Read the spec at docs/specs/s1a.5-attester-verify.spec.md first.

The verify command reads a JSON file, computes keccak256 of the serial,
queries getDevice on-chain, and prints VERIFIED or UNVERIFIED based on
address match. No signature verification.
Run against Anvil with both registered and unregistered cases.
```
