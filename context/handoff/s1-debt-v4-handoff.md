# Handoff: S1-Debt-V4 — Dev Config Extraction

## Context

The attester binary hardcodes a well-known Anvil private key (`0x59c6...`) and localhost RPC URL via `protocol/src/dev_config.rs`. This is a security risk if run against a real network. Replace with environment variables and delete the dev_config module entirely.

## Spec

Read `docs/specs/s1-debt-v4-dev-config-extraction.spec.md` for full details — it contains the exact current code and target code for every file.

## Task

1. **Baseline:** Run `just e2e-the-wire` — confirm it passes before changes

2. **Test:** Add `register_without_private_key_env_shows_clear_error` test in `attester/src/main.rs` — uses `.env_remove("HARDTRUST_PRIVATE_KEY")` to ensure the binary fails with a clear message when the env var is missing

3. **Implement in `attester/src/main.rs`:**
   - Remove `use hardtrust_protocol::{dev_config, Reading}` → `use hardtrust_protocol::Reading`
   - Replace `dev_config::DEV_PRIVATE_KEY` (line 78) with `std::env::var("HARDTRUST_PRIVATE_KEY")` — **required**, clear error if unset
   - Replace both `dev_config::DEV_RPC_URL` usages (lines 84 and 122) with `std::env::var("HARDTRUST_RPC_URL").unwrap_or_else(|_| "http://127.0.0.1:8545".to_string())` — **optional**, defaults to localhost
   - Note: `HARDTRUST_PRIVATE_KEY` is only needed for `register`, NOT for `verify`

4. **Delete `protocol/src/dev_config.rs`** entirely and remove `pub mod dev_config;` from `protocol/src/lib.rs`

5. **Update `scripts/e2e-the-wire.sh`:** Add these exports after line 8 (`sleep 2`), before any binary calls:
   ```bash
   export HARDTRUST_RPC_URL="http://127.0.0.1:8545"
   export HARDTRUST_PRIVATE_KEY="0x59c6995e998f97a5a0044966f0945389dc9e86dae88c7a8412f4603b6b78690d"
   ```

6. **Clean `justfile`:** If `DEV_SERIAL` and `DEV_ADDRESS` are no longer referenced by `e2e-the-wire.sh`, remove them from the justfile header (lines 4-5) and from the e2e recipe (lines 32-33)

7. **Update `README.md`:** Add Configuration section documenting `HARDTRUST_PRIVATE_KEY` (required for register) and `HARDTRUST_RPC_URL` (optional, defaults to localhost)

8. **Validate:** `just ci` and `just e2e-the-wire` both pass. Verify: `grep -r "dev_config" --include="*.rs" .` returns 0 results.

## Branch

`fix/s1-debt-dev-config`

## Acceptance

- `protocol/src/dev_config.rs` does not exist
- No `dev_config::` imports in non-test Rust code
- No hardcoded private key in attester source
- `HARDTRUST_PRIVATE_KEY` required — clear error if unset
- `HARDTRUST_RPC_URL` defaults to localhost
- New test passes
- `just ci` + `just e2e-the-wire` pass
- README documents env vars
