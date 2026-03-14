# Specs Index

Traceability map between user stories, specs, and handoff prompts.

## Setup & Infrastructure

| Spec ID | Name | Story | Status | Handoff |
|---------|------|-------|--------|---------|
| S0.1 | [Repo Initialization](s0.1-repo-initialization.spec.md) | — | Implemented | [s0.1-handoff](../handoff/s0.1-handoff.md) |
| S0.2 | [CI Pipeline](s0.2-ci-pipeline.spec.md) | — | Implemented | [s0.2-handoff](../handoff/s0.2-handoff.md) |
| S0.3 | [AI Workflow Setup](s0.3-ai-workflow-setup.spec.md) | — | Implemented | [s0.3-handoff](../handoff/s0.3-handoff.md) |
| S0.4 | [Seed ADRs](s0.4-seed-adrs.spec.md) | — | Implemented | [s0.4-handoff](../handoff/s0.4-handoff.md) |
| S0.5 | [AI Dev Practices](s0.5-ai-dev-practices.spec.md) | — | Implemented | [s0.5-handoff](../handoff/s0.5-handoff.md) |

## Slice 1 — Horizontal Sub-Slices

Implementation is sliced horizontally (end-to-end layers), not vertically (per story).
See [stories/slice-1/README.md](../stories/slice-1/README.md) for the sub-slice map and AC coverage.

### Slice 1a — "The Wire" (hardcoded end-to-end)

**Story:** [S1a — Verify Device Data Origin](../stories/slice-1/s1a-the-wire.md)

Orchestration handoff: [s1a-handoff](../handoff/s1a-handoff.md)

| Spec ID | Name | Status | Handoff |
|---------|------|--------|---------|
| S1a.1 | [Registry Contract](s1a.1-registry-contract.spec.md) | Draft | Embedded |
| S1a.2 | [Device Init](s1a.2-device-init.spec.md) | Draft | Embedded |
| S1a.3 | [Device Emit](s1a.3-device-emit.spec.md) | Draft | Embedded |
| S1a.4 | [Attester Register](s1a.4-attester-register.spec.md) | Draft | Embedded |
| S1a.5 | [Attester Verify](s1a.5-attester-verify.spec.md) | Draft | Embedded |
| S1a.6 | [Workspace + E2E](s1a.6-workspace-e2e.spec.md) | Draft | Embedded |

### Slice 1b — "Real Crypto" (real behavior)

| Spec ID | Name | Stories Touched | Status | Handoff |
|---------|------|-----------------|--------|---------|
| S1b | — | S1.1, S1.3, S1.4, S1.5, S1.6 | Pending | — |

### Slice 1c — "Polish & CI" (edge cases + CI)

| Spec ID | Name | Stories Touched | Status | Handoff |
|---------|------|-----------------|--------|---------|
| S1c | — | All S1.x edge cases + CI | Pending | — |

## User Stories Reference

### Slice 1 (Full Scope)

| Story | Name | File |
|-------|------|------|
| S1.1 | Generate Device Identity | [s1.1-generate-device-identity.md](../stories/slice-1/s1.1-generate-device-identity.md) |
| S1.2 | Deploy Contract + Seed Attester | [s1.2-deploy-contract.md](../stories/slice-1/s1.2-deploy-contract.md) |
| S1.3 | Register Device via CLI | [s1.3-register-device.md](../stories/slice-1/s1.3-register-device.md) |
| S1.4 | Query Device Status | [s1.4-query-device-status.md](../stories/slice-1/s1.4-query-device-status.md) |
| S1.5 | Emit Signed Reading | [s1.5-emit-signed-reading.md](../stories/slice-1/s1.5-emit-signed-reading.md) |
| S1.6 | Verify Device Data | [s1.6-verify-device-data.md](../stories/slice-1/s1.6-verify-device-data.md) |

### Slice 1a (Walking Skeleton)

| Story | Name | File |
|-------|------|------|
| S1a | Verify Device Data Origin (The Wire) | [s1a-the-wire.md](../stories/slice-1/s1a-the-wire.md) |

## Notes

- **Setup specs (S0.x)** have no user story — infrastructure/process prerequisites
- **Slice stories (S1a, S1b, S1c)** are horizontal sub-slices, each with its own user story
- **Full-scope stories (S1.1-S1.6)** define complete acceptance criteria; sub-slices cover them incrementally
- **Status:** Draft → Review → Approved → Implemented
- **Execution order:** S0.1 → S0.2 → S0.3 → S0.4 → S0.5 → S1a.1 → S1a.2 → S1a.3 → S1a.4 → S1a.5 → S1a.6 → S1b → S1c
