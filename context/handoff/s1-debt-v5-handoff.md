# Handoff: S1-Debt-V5 — Dependency Optimization & Code Quality

## Context

Several Cargo.toml files use overly broad feature flags (`"full"`), and there are minor code quality issues: missing PartialEq derive, inconsistent error handling, undocumented float precision concern.

## Spec

Read `docs/specs/s1-debt-v5-code-quality.spec.md` for full details.

## Task

1. In `attester/Cargo.toml`: replace `alloy features = ["full"]` with specific features (`["providers", "signers", "sol-types", "contract", "primitives"]`). Add features back only if compilation fails.
2. In `attester/Cargo.toml`: replace `tokio features = ["full"]` with `["rt-multi-thread", "macros"]`
3. In `device/Cargo.toml`: remove `serde` feature from `chrono`
4. In `protocol/src/error.rs`: add `PartialEq` to `ProtocolError` derive
5. In `device/src/main.rs`: replace `eprintln!` + `process::exit(1)` with `return Err(...)` in `run()`
6. In `protocol/src/crypto.rs` line ~30: add comment documenting float precision concern for Slice 2 on-chain verification
7. **Validate**: `cargo build --workspace`, `just ci`

## Branch

`fix/s1-debt-code-quality`

## Acceptance

- No `features = ["full"]` in any Cargo.toml
- No `serde` feature on chrono in device
- ProtocolError derives PartialEq
- No process::exit in run() functions
- Float precision documented
- `just ci` passes
