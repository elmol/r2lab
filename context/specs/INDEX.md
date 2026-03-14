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

## Slice 1 — CLI/Console

### S1a — "The Wire" (hardcoded end-to-end)

| Story | Specs | Status |
|-------|-------|--------|
| [S1a.1 — Register device on-chain](../stories/slice-1/s1a.1-register-device-on-chain.md) | TBD | Draft |
| [S1a.2 — Verify registered device](../stories/slice-1/s1a.2-verify-registered-device.md) | TBD | Draft |
| [S1a.3 — Verify unregistered device](../stories/slice-1/s1a.3-verify-unregistered-device.md) | TBD | Draft |

Orchestration handoff: [s1a-handoff](../handoff/s1a-handoff.md)

### S1b — "Real Crypto"

Stories and specs: TBD | **Status:** Pending

### S1c — "Polish & CI"

Stories and specs: TBD | **Status:** Pending

## Notes

- **Setup specs (S0.x)** have no user story — infrastructure/process prerequisites
- **Each story** gets one or more small specs (CÓMO) when ready for implementation
- **Status:** Draft → Review → Approved → Implemented
- **Execution order:** S0.1-S0.5 → S1a.1 → S1a.2 → S1a.3 → S1b → S1c
