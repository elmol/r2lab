# TerraGenesis — Specs Index

Traceability map for TerraGenesis-specific specs. HardTrust base specs (S0.x, S1.x) are inherited from upstream.

## Slice 2a — Device Capture

| Spec | Name | Story | Status | Handoff |
|------|------|-------|--------|---------|
| S2a.1-V1 | [Protocol Generic Signing](s2a.1-v1-protocol-generic-signing.spec.md) | S2a.1 | Implemented | Embedded |
| S2a.1-V2 | [Device Capture Command](s2a.1-v2-device-capture.spec.md) | S2a.1 | Implemented | Embedded |
| S2a.1-V3 | [Attester Verify Capture](s2a.1-v3-attester-verify-capture.spec.md) | S2a.1 | Approved | Pending |

### Execution Order

V1 first (protocol refactor). V2 depends on V1. V3 depends on V1 (uses Signable + generic verify).
