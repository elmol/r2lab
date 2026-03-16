# Project State

## Status
- **Phase:** 4/5/6 — Feature Specs + Implementation Handoff + Review (running iteratively)
- **Started:** 2026-03-12
- **Last session:** 2026-03-16

## Last Session Summary
S1b complete + S0.8 CI e2e implemented. Phase 6 global review: 3 sync issues fixed, project-state updated, HardTrust cleaned up.

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
| 4 — Feature Specs | 🔄 In Progress | — |
| 5 — Implementation Handoff | 🔄 In Progress (iterative with Phase 4) | — |
| 6 — Review & Validation | 🔄 First review done (infra) | 2026-03-14 |
| 7 — Production Readiness | ⏳ Pending | — |

## Discovery Artifacts
- `context/discovery.md` — Core discovery document
- `context/research-depin-device-identity.md` — Trend research (DePIN landscape, competitors, patterns)
- `context/ux-research-report.md` — UX research (personas, pain points, recommendations)

## Story Mapping Artifacts
- `context/story-map.md` — Full story map with 3 slices + Release 2

## Architecture Artifacts
- `context/architecture.md` — System architecture, components, data flow, crypto, CI/CD, dev workflow

## Stories Artifacts
- `context/stories/slice-1/` — Flat sub-slice stories (S1a + S1b defined, S1c TBD)

## Specs & Handoffs
- `context/specs/INDEX.md` — Traceability index (stories ↔ specs ↔ handoffs)
- `context/specs/` — 23 specs (S0.1-S0.8 implemented, S1a.1 V1-V4 implemented, S1a.2 V1-V3 implemented, S1a.3 V1 implemented, S1a-R implemented, S1b.1-S1b.3 V1-V2 implemented)
- `context/handoff/` — 8 handoff files (S0.1-S0.7, S1a overview)

## HardTrust Repo State
- Cargo workspace + Foundry project initialized
- CI running (GitHub Actions: lint, test, Solhint, integration stub, e2e stub)
- CLAUDE.md with AI dev practices, build order, Anvil context, pre-commit validation
- REVIEW.md with expanded review criteria
- 7 ADRs (including ADR-0007: no personal sign prefix)
- Slice 1 stories in docs/stories/slice-1/
- Architecture doc in docs/architecture.md
- All specs synced to docs/specs/ (S0.1-S0.8, S1a full, S1b full)
- HardTrustRegistry contract deployed (V1) with Solhint + Aderyn passing
- Real crypto: k256 keypair generation, ECDSA signing (sign_reading), ecrecover verification (verify_reading) in types/
- CI e2e: just e2e-the-wire runs on every push (GitHub Actions)
- Solhint + Aderyn configured for static analysis

## Open Questions
- Resolved: smart contract (single contract), storage (hybrid), crypto (secp256k1)
- Remaining open decisions deferred to feature specs (see architecture.md Section 10)

## Next Action
S1c (Polish & CI) story mapping.
