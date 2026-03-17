# Project State

## Status
- **Phase:** 4/5/6 — Feature Specs + Implementation Handoff + Review (running iteratively)
- **Started:** 2026-03-12
- **Last session:** 2026-03-18

## Last Session Summary
Phase 7 complete. S0.16 (version check script) and S0.17 (README update) merged. HardTrust main is clean — 51 specs synced, release pipeline working, README reflects full Phase 7 work. All phases through 7 closed.

## Current Product
- **Name:** HardTrust
- **One-liner:** A base layer for registering, certifying, and verifying physical hardware (RPi) in a DePIN network
- **Repo:** https://github.com/elmol/hardtrust

## Phase History
| Phase | Status | Completed |
|-------|--------|-----------|
| 1 — Discovery | ✅ Complete | 2026-03-12 |
| 2 — Story Mapping | ✅ Complete | 2026-03-13 |
| 3 — Architecture | ✅ Complete | 2026-03-13 |
| 4 — Feature Specs | ✅ Complete (Slice 1) | 2026-03-16 |
| 5 — Implementation Handoff | ✅ Complete (Slice 1) | 2026-03-16 |
| 6 — Review & Validation | ✅ Slice 1 closeout review done | 2026-03-16 |
| 7 — Production Readiness | ✅ Complete (Slice 1) | 2026-03-18 |

## Discovery Artifacts
- `context/discovery.md` — Core discovery document
- `context/research-depin-device-identity.md` — Trend research (DePIN landscape, competitors, patterns)
- `context/ux-research-report.md` — UX research (personas, pain points, recommendations)

## Story Mapping Artifacts
- `context/story-map.md` — Full story map with 3 slices + Release 2

## Architecture Artifacts
- `context/architecture.md` — System architecture, components, data flow, crypto, CI/CD, dev workflow

## Stories Artifacts
- `context/stories/slice-1/` — 9 stories across 3 sub-slices (S1a.1-3, S1b.1-3, S1c.1-3) — all complete

## Specs & Handoffs
- `context/specs/INDEX.md` — Traceability index (stories ↔ specs ↔ handoffs)
- `context/specs/` — 51 specs:
  - S0.1-S0.8 (8 infra specs, Implemented)
  - S1a.1 V1-V4, S1a.2 V1-V3, S1a.3 V1, S1a-R (9 specs, Implemented)
  - S1b.1 V1-V2, S1b.2 V1-V2, S1b.3 V1-V2 (6 specs, Implemented)
  - S1b-R1 to R5 (5 refactor specs, Implemented)
  - S1c.1 V1-V2, S1c.2 V1-V2, S1c.3 V1-V2 (6 specs, Implemented)
  - S1-Debt-V1 to V5 (5 tech debt specs, Implemented)
  - S0.9-S0.16 (8 Phase 7 release pipeline specs, Implemented)
  - S0.17 (1 README update spec, Implemented)
- `context/handoff/` — 15 handoff files (S0.1-S0.7, S0.9-S0.16, S1a overview, S1b-R5, S1-Debt-V1-V5)

## HardTrust Repo State
- Cargo workspace: `device/`, `attester/`, `protocol/` (hardtrust-protocol) + Foundry `contracts/`
- `protocol/` crate with internal modules: domain.rs, crypto.rs, error.rs, dev_config.rs
- CLI/core separation: device/ and attester/ each have lib.rs (pure logic) + main.rs (IO shell)
- Real crypto: k256 keypair generation, ECDSA signing (sign_reading), ecrecover verification (verify_reading)
- Real temperature: CPU sensor read with emulation fallback (ADR-0006)
- Contract hardening: DeviceAlreadyRegistered error, DeviceRegistered event, duplicate guard
- Error handling: Result-based, graceful failure across all CLI commands
- 8 ADRs (ADR-0001 through ADR-0008)
- CI: lint, test, Solhint, e2e-the-wire on every push (GitHub Actions)
- 51 Rust tests (pass) + 3 ignored + 11 Forge tests (pass)
- All specs synced to docs/specs/ (S0.x, S1a, S1b, S1c)
- All stories synced to docs/stories/slice-1/

## Tech Debt (before Slice 2)
- ~~**3 #[ignore] tests**~~ — resolved in S1-Debt-V1
- ~~**Error parsing coupled to CLI**~~ — resolved in S1-Debt-V1
- **Historical spec references**: 13 specs in docs/specs/ reference old crate names (hardtrust-types, hardtrust-core) — correct at time of writing, superseded by S1b-R1 and S1b-R5

## Tech Debt (Phase 7 — release infrastructure)
- **`release_version` hardcoded in multiple places**: `justfile` has `release_version := "v0.1.0"` which must be updated manually on every release. Should be read dynamically from the workspace `Cargo.toml` (single source of truth). After `cargo release` bumps the version in `Cargo.toml`, the justfile variable becomes stale until manually updated. Fix: read version from `Cargo.toml` in justfile via `$(grep '^version' Cargo.toml | head -1 | sed 's/version = "\(.*\)"/\1/')` or equivalent.

## Open Questions
- Resolved: smart contract (single contract), storage (hybrid), crypto (secp256k1)
- Resolved: on-chain ecrecover deferred to Slice 2 (no CLI consumer)
- Remaining open decisions deferred to feature specs (see architecture.md Section 10)

## Next Action
Slice 2 planning — Web Portal (attester webapp + public verification page). Start Phase 4 Feature Specs for Slice 2 stories.
