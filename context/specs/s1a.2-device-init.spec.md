# Spec: S1a.2 — Device Init (Hardcoded)

## Goal

Create the `device` binary with an `init` subcommand that prints a hardcoded device identity. No crypto, no file I/O.

## Story Reference

- **S1a** — Verify Device Data Origin (The Wire)
- Supports S1a AC-1 and AC-2 (device must have identity for verification to work)

## Related ADRs

- ADR-0001: Monorepo flat structure

## What to Build

### New crate: `device/`

Added to the workspace `Cargo.toml`:

```toml
[workspace]
members = ["device"]
```

**Dependencies:** `clap` (CLI), `serde` + `serde_json` (for emit in next spec), `chrono` (for emit in next spec).

No dependency on `common/`, `alloy`, or any crypto crate.

### `device init` behavior

- Prints to stdout:
  ```
  Serial: HARDCODED-001
  Address: 0x1234567890abcdef1234567890abcdef12345678
  ```
- The address is a fixed, valid Ethereum address (0x + 40 hex chars). Use the same address consistently across init and emit.
- No file I/O. No key generation. Print and exit.

### Tests

- Unit test: `init` output contains serial and address in expected format

## What NOT to Build

- Key generation or crypto
- File persistence
- Emulation mode flag
- Existing keys guard
- JSON output format

## Validation

```bash
cargo run --bin device -- init
# Expected:
# Serial: HARDCODED-001
# Address: 0x1234567890abcdef1234567890abcdef12345678
```

## Handoff Prompt

```
Create the device binary with a hardcoded init command for HardTrust (Slice 1a).

Read the spec at docs/specs/s1a.2-device-init.spec.md first.

Create device/ as a new workspace member. The init subcommand prints a hardcoded
serial and Ethereum address. No crypto, no file I/O, no common/ crate.
Run cargo test and verify the output format.
```
