# Spec: S1a.3 — Device Emit (Hardcoded)

## Goal

Add an `emit` subcommand to the `device` binary that writes a hardcoded data reading to a JSON file.

## Story Reference

- **S1a** — Verify Device Data Origin (The Wire)
- Covers S1.5 AC-1 partial (hardcoded data, fake signature, real timestamp)

## Related ADRs

- ADR-0005: Hybrid storage (readings off-chain as JSON files)

## What to Build

### `device emit` behavior

- Writes a file `reading.json` to the current directory
- Contents: single JSON object (not array)
- Timestamp is real (current UTC, ISO 8601)
- All other fields are hardcoded
- Prints confirmation: `Reading written to reading.json`

### JSON format for `reading.json`

```json
{
  "serial": "HARDCODED-001",
  "address": "0x1234567890abcdef1234567890abcdef12345678",
  "temperature": 36.5,
  "timestamp": "2025-01-15T14:30:00Z",
  "signature": "0xFAKESIG0000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000"
}
```

Field notes:
- `serial`: same hardcoded value as `device init`
- `address`: same hardcoded Ethereum address as `device init`
- `temperature`: hardcoded `36.5` (float)
- `timestamp`: real current UTC time in ISO 8601
- `signature`: fake hex string, 65 bytes (130 hex chars) prefixed with `0x`

### Tests

- Unit test: emit produces valid JSON with all required fields and correct types

## What NOT to Build

- Real signing
- Real temperature reading
- Identity check (no keypair guard)
- Append to existing file

## Validation

```bash
cargo run --bin device -- emit
cat reading.json
# Expected: valid JSON with serial, address, temperature, timestamp, signature
```

## Handoff Prompt

```
Add the emit subcommand to the device binary for HardTrust (Slice 1a).

Read the spec at docs/specs/s1a.3-device-emit.spec.md first.

The emit command writes reading.json with hardcoded data and a fake signature.
Only the timestamp is real (current UTC). Run cargo test and verify the JSON structure.
```
