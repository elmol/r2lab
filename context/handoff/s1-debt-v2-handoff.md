# Handoff: S1-Debt-V2 — CI Pipeline Fix & Docs Sync

## Context

CI has a silent failure bug: `2>/dev/null || echo` swallows cargo errors, and `forge build` doesn't run before `cargo test` (attester needs `contracts/out/`). Also `docs/architecture.md` still references the old `common/` crate.

## Spec

Read `docs/specs/s1-debt-v2-ci-docs-fix.spec.md` for full details.

## Task

1. In `.github/workflows/ci.yml` **test job**: add `forge build` step before `cargo test`, remove `2>/dev/null || echo` from all cargo commands
2. In `.github/workflows/ci.yml` **lint job**: remove `2>/dev/null || echo` from cargo fmt/clippy
3. In `docs/architecture.md`: replace all `common/` references with `protocol/`
4. In `README.md`: add `aderyn` to prerequisites (note as optional/local-only)
5. **Validate**: push branch, verify CI logs show actual test output

## Branch

`fix/s1-debt-ci-docs`

## Acceptance

- CI logs show real `cargo test` output (not swallowed)
- `forge build` runs before `cargo test`
- Zero `common/` references in `docs/architecture.md`
- `just ci` passes locally
