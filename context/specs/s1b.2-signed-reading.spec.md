# Spec: S1b.2 — Signed Reading

## Parent Story

S1b.2 — Signed Reading

## Goal

`device emit` produces a real ECDSA signature over the reading payload. The private key loaded from `~/.hardtrust/device.key` (written by `device init` in S1b.1) signs a canonical encoding of the reading fields. The output `reading.json` contains a 132-char hex signature verifiable by any party holding the device's public key.

This story adds signing only — no verification is built here.

## Dependencies

**S1b.1 must be merged before this story begins.** This spec depends on:
- `~/.hardtrust/device.key` existing (written by `device init`)
- `public_key_to_address` function available in `common/`
- `k256` crate already present in `common/Cargo.toml`

## What to Build

### Part 1: `sign_reading` function in `common/`

#### `common/src/lib.rs`

Add a pure, I/O-free function:

```rust
pub fn sign_reading(key: &k256::ecdsa::SigningKey, reading: &Reading) -> Result<String, Error>
```

- Takes a signing key and a `Reading` (with all fields populated, including `timestamp` as ISO 8601 string)
- Returns a hex-encoded 65-byte recoverable ECDSA signature as `"0x<130 hex chars>"`
- No I/O, no side effects — pure function, fully unit-testable

#### Canonical signing payload (must be exact for verifier interoperability)

Compute: `keccak256(serial_hash || address_bytes || temperature_scaled_be || timestamp_be)`

Where:
- `serial_hash`: `keccak256(serial.as_bytes())` — 32 bytes
- `address_bytes`: 20-byte Ethereum address derived from the signing key's public key (via `public_key_to_address`)
- `temperature_scaled_be`: `(temperature * 1000.0) as i64` encoded as 8 bytes big-endian (`i64::to_be_bytes`)
- `timestamp_be`: Unix timestamp (`u64`) parsed from the ISO 8601 timestamp string, encoded as 8 bytes big-endian (`u64::to_be_bytes`)

Concatenate in the order above (32 + 20 + 8 + 8 = 68 bytes), then keccak256 the result to get the 32-byte message hash.

Sign the 32-byte hash using `k256::ecdsa::SigningKey::sign_prehash_recoverable`.

#### Signature encoding

Encode as 65-byte EVM format: `r (32 bytes) ++ s (32 bytes) ++ v (1 byte)`

- `v` = recovery id as raw value: `0` or `1` (no Ethereum legacy offset of 27/28)
- Final string: `"0x"` + lowercase hex of those 65 bytes = 132 characters total

Do NOT use Ethereum personal sign prefix (`\x19Ethereum Signed Message\n32`). Raw prehash signing only.

#### `common/Cargo.toml`

- `k256` must include features `["ecdsa"]` (already added in S1b.1 — verify, do not duplicate)
- `sha3` or `tiny-keccak` for keccak256 (whichever was chosen in S1b.1 — remain consistent)
- `chrono` for parsing ISO 8601 timestamp to Unix timestamp

#### Unit tests in `common/src/lib.rs`

- `sign_reading` with a known deterministic key and fixed inputs produces a 132-char signature string starting with `"0x"`
- Canonical payload encoding is deterministic: same inputs always produce the same 68-byte preimage
- Signature is recoverable: public key can be recovered from (hash, signature) — verify using `k256::ecdsa::VerifyingKey::recover_from_prehash`

### Part 2: `device emit` — real signing

#### Updated `device/src/main.rs`

Replace hardcoded values with real ones in the `emit` subcommand:

1. Load private key from `~/.hardtrust/device.key` (same path and format as written by `device init` in S1b.1)
2. Read serial from `/sys/firmware/devicetree/base/serial-number` — use emulation fallback `"EMU-<hostname>"` if file does not exist (same fallback as S1b.1)
3. Derive Ethereum address from loaded key using `public_key_to_address` (from `common/`)
4. Build a `Reading` with:
   - `serial`: from step 2
   - `address`: from step 3
   - `temperature`: `42.0` (unchanged)
   - `timestamp`: `Utc::now().to_rfc3339()`
   - `signature`: `"0xPENDING"` (placeholder — will be replaced)
5. Call `sign_reading(&key, &reading)` from `common/` to produce the real signature
6. Set `reading.signature` to the returned hex string
7. Write `reading.json` to current directory
8. Print `"Wrote reading.json"`

If `~/.hardtrust/device.key` does not exist, print a clear error and exit with a non-zero code:
```
Error: device key not found. Run `device init` first.
```

#### Integration test in `device/src/main.rs`

- Run `device init` then `device emit`
- Read back `reading.json`
- Assert `signature` field is 132 characters long and starts with `"0x"`
- Assert `signature` is not `"0xFAKESIG"` and not `"0xPENDING"`

### Output Files

| File | Change |
|------|--------|
| `common/src/lib.rs` | Add `sign_reading` function and canonical payload encoding |
| `common/Cargo.toml` | Verify `k256`, `sha3`/`tiny-keccak`, `chrono` dependencies — add if missing |
| `device/src/main.rs` | Load key, read serial, derive address, call `sign_reading`, write `reading.json` |

No new files. No changes to `types/src/lib.rs` — the `Reading` struct fields are unchanged.

## What NOT to Build

- No signature verification (that is S1b.3)
- No changes to the attester
- No changes to the contract
- No changes to the `Reading` struct fields — `signature: String` already exists
- No Ethereum personal sign prefix — raw prehash only
- No configurable key path — always `~/.hardtrust/device.key`
- No configurable output path — always `reading.json` in current directory
- No changes to `device init` — that is S1b.1

## Validation

```bash
device init                # generates key if not present (S1b.1)
device emit                # writes reading.json with real signature
cat reading.json           # signature field: "0x<130 hex chars>", not "0xFAKESIG"
just build
just test
```

Expected `reading.json` shape:

```json
{
  "serial": "EMU-<hostname>",
  "address": "0x<40 hex chars>",
  "temperature": 42.0,
  "timestamp": "<ISO 8601 UTC>",
  "signature": "0x<130 hex chars>"
}
```

## Handoff Prompt

```
Implement real ECDSA signing in `device emit` for HardTrust.

S1b.1 must be merged before starting this branch.

Read the spec at docs/specs/s1b.2-signed-reading.spec.md first.

Output files:
- common/src/lib.rs (add sign_reading function and canonical payload encoding)
- common/Cargo.toml (verify k256, keccak, chrono dependencies)
- device/src/main.rs (load key, read serial, derive address, sign, write reading.json)

Respect the "What NOT to Build" section strictly.
After implementation, run `just build && just test` and manually verify:
  device init && device emit && cat reading.json
The signature field must be "0x" followed by exactly 130 hex characters.
Branch: feat/s1b.2-signed-reading
```
