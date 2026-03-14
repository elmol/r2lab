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

Each sub-slice is a horizontal end-to-end pass with its own user story and specs.
See [stories/slice-1/README.md](../stories/slice-1/README.md) for the sub-slice map.

### S1a — "The Wire" (hardcoded end-to-end)

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

### S1b — "Real Crypto"

**Story:** TBD | **Status:** Pending

### S1c — "Polish & CI"

**Story:** TBD | **Status:** Pending

## Notes

- **Setup specs (S0.x)** have no user story — infrastructure/process prerequisites
- **Slice stories (S1a, S1b, S1c)** are flat, independent user stories — each sub-slice owns its ACs
- **Status:** Draft → Review → Approved → Implemented
- **Execution order:** S0.1 → S0.2 → S0.3 → S0.4 → S0.5 → S1a.1 → S1a.2 → S1a.3 → S1a.4 → S1a.5 → S1a.6 → S1b → S1c
