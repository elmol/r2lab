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

## Slice 1 — CLI End-to-End

| Spec ID | Name | Story | Status | Handoff |
|---------|------|-------|--------|---------|
| S1.1 | [Device Init](s1.1-device-init.spec.md) | [Generate Device Identity](../stories/slice-1/s1.1-generate-device-identity.md) | Draft | — |
| S1.2 | — | [Deploy Contract + Seed Attester](../stories/slice-1/s1.2-deploy-contract.md) | Pending | — |
| S1.3 | — | [Register Device via CLI](../stories/slice-1/s1.3-register-device.md) | Pending | — |
| S1.4 | — | [Query Device Status](../stories/slice-1/s1.4-query-device-status.md) | Pending | — |
| S1.5 | — | [Emit Signed Reading](../stories/slice-1/s1.5-emit-signed-reading.md) | Pending | — |
| S1.6 | — | [Verify Device Data](../stories/slice-1/s1.6-verify-device-data.md) | Pending | — |

## Notes

- **Setup specs (S0.x)** have no user story — they are infrastructure/process prerequisites
- **Functional specs (S1.x+)** map 1:1 to refined user stories, but a story may produce multiple specs if needed
- **Status:** Draft → Review → Approved → Implemented
- **Handoff:** link to the prompt document in `context/handoff/`
- **Execution order:** S0.1 → S0.2 → S0.3 → S0.4 → S0.5 → S1.1 → ...
