# Spec: S1b.3 — Real Verify

## Parent Story

S1b.3 — Real Verify (Cryptographic Signature Verification in Attester)

## Goal

Replace the address-equality check in `attester verify` with real ECDSA signature recovery. The attester reconstructs the canonical payload hash from the reading fields, recovers the signer address using `ecrecover`, and compares it against the on-chain registered address — so that a forged reading with the correct `address` field but a wrong or missing signature fails verification.

## What to Build

### Prerequisite: S1b.1 and S1b.2 must be merged first

This spec depends on:
- S1b.1: real keypair generation (device generates a real ECDSA key; `register` uses the derived address)
- S1b.2: real ECDSA signatures (device `emit` produces a `reading.json` with a `signature` field signed over the canonical payload hash)

The `verify_reading` function defined here must consume exactly the same canonical encoding that S1b.2's `sign_reading` produces.

### New function `verify_reading` in `common/src/lib.rs`

```rust
pub fn verify_reading(reading: &Reading, on_chain_address: Address) -> bool
```

Behavior:
1. Reject immediately if `on_chain_address == Address::ZERO` (unregistered device) → return `false`
2. Parse `reading.signature` as a 65-byte hex string → Alloy `Signature` type. Return `false` on parse error.
3. Reconstruct the canonical payload hash (must match S1b.2 exactly):
   - `serial_hash`: `keccak256(reading.serial.as_bytes())` → 32 bytes
   - `address_bytes`: parse `reading.address` as 20-byte `Address`
   - `temperature_scaled_be`: `(reading.temperature * 1000.0) as i64` as 8-byte big-endian
   - `timestamp_be`: parse ISO 8601 timestamp → Unix `u64` → 8-byte big-endian
   - `prehash`: `keccak256(serial_hash || address_bytes || temperature_scaled_be || timestamp_be)`
4. Recover the signer: `Signature::recover_address_from_prehash(&prehash)` → return `false` on error
5. Return `true` if `recovered_address == on_chain_address`, `false` otherwise

This function is pure: no I/O, no network calls. It takes a `Reading` and an `Address` and returns `bool`.

### Remove `is_verified` from `attester/src/main.rs`

Delete the existing `is_verified()` function. Replace its call site with `common::verify_reading()`.

### Updated `verify` subcommand flow in `attester/src/main.rs`

The flow stays the same as S1a.2, with one change at step 6:

1. Read `--file` path to string
2. Deserialize to `Reading` struct (from `hardtrust-types`)
3. Compute `keccak256(reading.serial.as_bytes())` for the serial hash (used for contract query)
4. Query contract `getDevice(serialHash)` — no change from S1a.2
5. Call `common::verify_reading(&reading, on_chain_address)` instead of `is_verified()`
6. Print `VERIFIED` or `UNVERIFIED` — output format unchanged

### Unit tests for `verify_reading` in `common/`

- Sign a reading with `sign_reading(key, reading)` (from S1b.2), then call `verify_reading(reading_with_sig, derived_address)` → `true`
- Tamper with `reading.temperature` after signing → `verify_reading` → `false`
- Tamper with `reading.signature` (corrupt one byte) → `verify_reading` → `false`
- `on_chain_address` is `Address::ZERO` → `false` (unregistered device)

### New ADR: `docs/adr/0007-signing-no-personal-sign-prefix.md`

Create this ADR in the HardTrust repository:

- **Context:** Device readings are machine-to-machine payloads, not user messages. EIP-191 personal sign prefix (`\x19Ethereum Signed Message:\n`) is designed to prevent signature reuse attacks in human-facing wallet operations, not for device telemetry.
- **Decision:** Use raw keccak256 prehash with no prefix. The canonical payload encoding (tuple of serial_hash + address + temperature_scaled + timestamp) provides domain separation without any prefix.
- **Consequences:** On-chain `ecrecover` in S1c can use the same raw hash without prefix handling. The signing and verification algorithm is symmetric across S1b.2 (`sign_reading`) and S1b.3 (`verify_reading`).

### Output files

| File | Change |
|------|--------|
| `attester/src/main.rs` | Remove `is_verified()`; call `common::verify_reading()` instead |
| `common/src/lib.rs` | Add `verify_reading` function with canonical payload hash reconstruction |
| `docs/adr/0007-signing-no-personal-sign-prefix.md` | New ADR |

## What NOT to Build

- No on-chain ecrecover — that is S1c
- No changes to the `device` binary
- No changes to the contract
- No Ethereum personal sign prefix (EIP-191) — raw prehash only (see ADR-0007)
- No new output formats — `VERIFIED` / `UNVERIFIED` on stdout, unchanged
- No configurable RPC URL
- No error handling beyond `.expect()` or early-return `false` with descriptive messages

## Validation

```bash
just build
just test                   # unit tests pass including all tamper tests

just e2e-the-wire           # The Wire gate: PASSED

# Manual tamper test (UNVERIFIED path):
device emit
# edit reading.json: change temperature value (e.g. 22.5 → 99.9)
attester verify --file reading.json --contract <addr>
# expected output: UNVERIFIED
```

## Handoff Prompt

```
Replace the address-equality check in attester verify with real ECDSA signature recovery.

Read the spec at docs/specs/s1b.3-real-verify.spec.md first.
S1b.1 and S1b.2 must be merged before starting this story — verify_reading must
consume exactly the same canonical payload encoding that sign_reading (S1b.2) produces.

Output files:
- Updated attester/src/main.rs (remove is_verified, call common::verify_reading)
- Updated common/src/lib.rs (add verify_reading with canonical hash reconstruction)
- New docs/adr/0007-signing-no-personal-sign-prefix.md

The verify_reading function must be pure (no I/O). Canonical payload reconstruction
must match S1b.2 exactly: serial_hash || address_bytes || temperature_scaled_be ||
timestamp_be, then keccak256. Use Alloy's Signature::recover_address_from_prehash.
No Ethereum personal sign prefix (see ADR-0007).

Unit tests required: sign then verify → true; tamper temperature → false;
tamper signature → false; Address::ZERO → false.

Respect the "What NOT to Build" section strictly.
After implementation, run `just build && just test && just e2e-the-wire` to validate.
Branch: feat/s1b.3-real-verify
```
