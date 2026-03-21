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
| S2a.3-V1 | [E2E Capture Cases](s2a.3-v1-e2e-capture.spec.md) | — | Approved | Pending |

## Slice 2a — Review Debt

| Spec | Name | Story | Status | Handoff |
|------|------|-------|--------|---------|
| S2a-Debt-V1 | [Review Fixes](s2a-debt-v1-review-fixes.spec.md) | — | Approved | Pending |

### Execution Order

S2a.1: V1 first (protocol). V2 depends on V1. V3 depends on V1.
S2a.2: V1 first (capture script). V2 depends on V1 (needs the script to install).
S2a-Debt: After all S2a specs implemented. Single spec covering 6 fixes.
