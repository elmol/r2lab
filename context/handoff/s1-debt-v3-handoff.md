# Handoff: S1-Debt-V3 — Dead Code & Contract Cleanup

## Context

Several pieces of dead code remain from scaffolding and development: Counter.sol (Foundry template), unused OWNER in contract, phantom --force flag, unused dev_config constants.

## Spec

Read `docs/specs/s1-debt-v3-dead-code-cleanup.spec.md` for full details.

## Task

1. Delete `contracts/src/Counter.sol` and `contracts/test/Counter.t.sol` (if exists)
2. In `device/src/main.rs`: change `--force` message to `"Delete {} to regenerate."`
3. In `contracts/src/HardTrustRegistry.sol`: remove `OWNER` state variable and constructor assignment
4. In `protocol/src/dev_config.rs`: remove `DEV_SERIAL` and `DEV_ADDRESS` constants, fix stale comments
5. Check `scripts/e2e-the-wire.sh` — if it references DEV_SERIAL/DEV_ADDRESS from Rust, inline the values in the script
6. **Validate**: `forge test`, `just ci`, `just e2e-the-wire` all pass

## Branch

`fix/s1-debt-dead-code`

## Acceptance

- No Counter.sol in codebase
- No --force mentioned in device output
- No OWNER in contract
- No DEV_SERIAL/DEV_ADDRESS in dev_config.rs
- All tests pass
