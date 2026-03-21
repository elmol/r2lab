# TerraGenesis — Specs Index

Traceability map for TerraGenesis-specific specs. HardTrust base specs (S0.x, S1.x) are inherited from upstream.

## Slice 2a — Device Capture

| Spec | Name | Story | Status | Handoff |
|------|------|-------|--------|---------|
| S2a.1-V1 | [Protocol Generic Signing](s2a.1-v1-protocol-generic-signing.spec.md) | S2a.1 | Implemented | Embedded |
| S2a.1-V2 | [Device Capture Command](s2a.1-v2-device-capture.spec.md) | S2a.1 | Implemented | Embedded |
| S2a.1-V3 | [Attester Verify Capture](s2a.1-v3-attester-verify-capture.spec.md) | S2a.1 | Implemented | Embedded |

## Slice 2a — Capture Script & Install

| Spec | Name | Story | Status | Handoff |
|------|------|-------|--------|---------|
| S2a.2-V1 | [TerraScope Capture Script](s2a.2-v1-capture-script.spec.md) | S2a.2 | Implemented | Embedded |
| S2a.2-V2 | [Default --cmd + Install](s2a.2-v2-default-cmd-and-install.spec.md) | S2a.2 | Implemented | Embedded |

## Slice 2a — E2E Capture

| Spec | Name | Story | Status | Handoff |
|------|------|-------|--------|---------|
| S2a.3-V1 | [E2E Capture Cases](s2a.3-v1-e2e-capture.spec.md) | — | Implemented | Embedded |

## Slice 2a — Environment Attestation

| Spec | Name | Story | Status | Handoff |
|------|------|-------|--------|---------|
| S2a.4-V1 | [Environment Attestation](s2a.4-v1-environment-attestation.spec.md) | S2a.4 | Implemented | Embedded |
| S2a.4-V2 | [Embedded Release Hashes](s2a.4-v2-embedded-release-hashes.spec.md) | S2a.4 | Implemented | Embedded |

## Sync & Documentation

| Spec | Name | Story | Status | Handoff |
|------|------|-------|--------|---------|
| S0.20 | [Full Sync, README & Cleanup](s0.20-terragenesis-sync-readme.spec.md) | — | Implemented | Embedded |
| S0.21 | [Release v0.3.0](s0.21-release-v0.3.0.spec.md) | — | Draft | Embedded |

## Slice 2a — Review Debt

| Spec | Name | Story | Status | Handoff |
|------|------|-------|--------|---------|
| S2a-Debt-V1 | [Review Fixes](s2a-debt-v1-review-fixes.spec.md) | — | Implemented | Embedded |

## Slice 2b — On-Chain Verification

| Spec | Name | Story | Status | Handoff |
|------|------|-------|--------|---------|
| S2b.1-V1 | [On-Chain Verify (view function)](s2b.1-v1-onchain-verify.spec.md) | S2b.1 | Implemented | Embedded |
| S2b.1-V2 | [On-Chain Environment Hashes](s2b.1-v2-onchain-env-hashes.spec.md) | S2b.1 | Implemented | Embedded |
| S2b-Debt-V1 | [Unify verifyCapture + verifyEnvironment](s2b-debt-v1-unify-verify.spec.md) | S2b.1 | Implemented | Embedded |
| S2b-Debt-V2 | [Fix trailing null byte in serial](s2b-debt-v2-serial-null-byte.spec.md) | — | Implemented | Embedded |
| S2b.2 | On-Chain Submit Proof (persistence) | S2b.2 | Future | — |
| ADR-0010 | [On-Chain Verification Model](adr-0010-onchain-verification-model.md) | — | Proposed | — |

## Slice 2c — Web Portal

| Spec | Name | Story | Status | Handoff |
|------|------|-------|--------|---------|
| S2c-Web-V1 | [Registry Web Portal (Retrospective)](s2c-web-v1-registry-portal.spec.md) | S2c | Implemented (manual) | — |
| S0.22 | [README Update: Web Portal](s0.22-readme-web-update.spec.md) | — | Draft | Embedded |

### Execution Order

S2a.1: V1 first (protocol). V2 depends on V1. V3 depends on V1.
S2a.2: V1 first (capture script). V2 depends on V1 (needs the script to install).
S2a-Debt: After all S2a specs implemented. Single spec covering 6 fixes.
