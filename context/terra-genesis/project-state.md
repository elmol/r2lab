# TerraGenesis — Project State

## Status
- **Phase:** 4/5/6 — Feature Specs + Implementation Handoff + Review (iterative)
- **Started:** 2026-03-20
- **Last session:** 2026-03-21

## Last Session Summary
Slice 2b fully implemented: S2b.1-V1 (on-chain verifyCapture), S2b.1-V2 (env hashes), S2b-Debt-V1 (unified 6-param verifyCapture), S2b-Debt-V2 (RPi serial null byte fix). All 54 tests pass. Full sync audit done — README needs update for new features, version bump 0.1.0→0.3.0 pending. S0.21 release spec created.

## Current Product
- **Name:** TerraGenesis
- **One-liner:** Proof-of-Physical-Data layer for TerraScope microscopes — fork of HardTrust for DeSci hackathon
- **Repo:** https://github.com/biotexturas/terra-genesis
- **Org:** biotexturas

## Phase History
| Phase | Status | Completed |
|-------|--------|-----------|
| 1 — Discovery | ✅ Inherited from HardTrust | — |
| 2 — Story Mapping | ✅ Complete (Slice 2a + 2b) | 2026-03-21 |
| 3 — Architecture | ✅ Inherited + ADR-0009, ADR-0010 | 2026-03-21 |
| 4 — Feature Specs | ✅ Complete (Slice 2a + 2b) | 2026-03-21 |
| 5 — Implementation Handoff | ✅ Complete (Slice 2a + 2b) | 2026-03-21 |
| 6 — Review & Validation | ✅ Complete (Slice 2a + 2b) | 2026-03-21 |

## Story Mapping Artifacts
- `context/terra-genesis/story-map.md` — Story map with Slice 2a (Device Capture), 2a.4 (Environment Attestation), 2b (On-Chain Verify), 2c (On-Chain Submit, future)

## Stories Artifacts
- `context/terra-genesis/stories/slice-2/s2a.1-device-capture.md`
- `context/terra-genesis/stories/slice-2/s2a.2-capture-script.md`
- `context/terra-genesis/stories/slice-2/s2a.4-capture-environment-attestation.md`
- `context/terra-genesis/stories/slice-2/s2b.1-onchain-verify.md`

## Specs & Handoffs
- `context/terra-genesis/specs/INDEX.md` — Traceability index
- 14 specs total:
  - **Slice 2a (all Implemented):**
    - S2a.1-V1 Protocol Generic Signing
    - S2a.1-V2 Device Capture Command
    - S2a.1-V3 Attester Verify Capture
    - S2a.2-V1 TerraScope Capture Script
    - S2a.2-V2 Default --cmd + Install
    - S2a.3-V1 E2E Capture Cases
    - S2a.4-V1 Environment Attestation
    - S2a.4-V2 Embedded Release Hashes
    - S2a-Debt-V1 Review Fixes
    - S0.20 Full Sync, README & Cleanup
  - **Slice 2b:**
    - S2b.1-V1 On-Chain Verify (Implemented)
    - S2b.1-V2 On-Chain Env Hashes (Implemented)
    - S2b-Debt-V1 Unify verifyCapture (Implemented)
    - S2b-Debt-V2 Serial Null Byte Fix (Implemented)
    - S2b.2 On-Chain Submit Proof (Future)
  - **Sync & Release:**
    - S0.21 Release v0.3.0 (Draft)
  - **ADRs:**
    - ADR-0009 Capture Script Hardware Adapter
    - ADR-0010 On-Chain Verification Model (Proposed)

## TerraGenesis Repo State
- Fork of HardTrust with capture + on-chain verify
- Cargo workspace: `device/`, `attester/`, `protocol/`, `terrascope/`, Foundry `contracts/`
- OpenZeppelin ECDSA for on-chain signature verification
- `verifyCapture()` view function — trustless, free, permissionless
- `registeredDevices` reverse lookup mapping
- Signable trait: generic sign/verify for Reading + Capture types
- Capture with CaptureEnvironment (script_hash, binary_hash, hw_serial, camera_info)
- Embedded SHA256SUMS for default environment verification
- E2E: 6 cases (reading +/-, capture +/-, env match/mismatch)
- v0.2.0 released with Slice 2a

## Next Action
Execute S0.21 handoff — README update, version bump to 0.3.0, SHA256SUMS, tag v0.3.0. Future: S2b.2 (submit proof persistence), Option B (Merkle prehash for trustless env).
