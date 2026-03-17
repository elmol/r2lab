# Handoff: S1-Debt-V1 — Extract Registration Error Classification

## Context

You are implementing a tech debt refactor in HardTrust. The `DeviceAlreadyRegistered` error detection is currently inline in `attester/src/main.rs` (the IO shell), making it untestable without Anvil. There are also 3 `#[ignore]` tests that never run in CI.

## Spec

Read `docs/specs/s1-debt-v1-registration-error-extract.spec.md` for full details.

## Task

1. **TDD — write failing tests first** in `attester/src/lib.rs`:
   - `classify_error_detects_already_registered_by_name`
   - `classify_error_detects_already_registered_by_selector`
   - `classify_error_returns_transaction_failed_for_other_errors`

2. **Implement** `RegistrationError` enum and `classify_registration_error()` in `attester/src/lib.rs`

3. **Refactor** `attester/src/main.rs`:
   - Replace inline error detection (lines 91–98) with call to `classify_registration_error`
   - Delete the 3 `#[ignore]` tests: `verify_registered_device`, `register_duplicate_shows_human_error`, `register_success_shows_confirmation`

4. **Validate**: `just ci` and `just e2e-the-wire` both pass

## Branch

`fix/s1-debt-registration-error-extract`

## Acceptance

- 0 `#[ignore]` tests in the codebase
- 3 new unit tests pass without Anvil
- CLI output unchanged
- `just ci` passes
