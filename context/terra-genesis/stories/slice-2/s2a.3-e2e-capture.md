# S2a.3 — E2E Capture Verification

## User Story

As a developer, I want the e2e test to verify the full capture flow (device capture → attester verify) using an emulated capture script, so that I have confidence the capture pipeline works end-to-end without camera hardware.

## Acceptance Criteria

1. `just e2e-the-wire` includes two new cases for capture alongside the existing reading cases
2. Case 3: `device capture` with mock script → `attester verify` → VERIFIED
3. Case 4: fake capture.json → `attester verify` → UNVERIFIED
4. A mock capture script (`scripts/mock-capture.sh`) produces deterministic test files without camera hardware
5. The mock script follows the same contract as `terrascope/capture.sh` (ADR-0009): takes `$1` as output dir, writes files there
6. The e2e passes in CI (no hardware dependencies)
7. Existing reading cases (1 and 2) remain unchanged

## Out of Scope

- Real camera capture in e2e
- Deprecating `emit` (future)
