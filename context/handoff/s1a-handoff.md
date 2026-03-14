# Handoff: S1a — The Wire

## Pre-requisites

- S0.1-S0.5 implemented (repo, CI, AI workflow, ADRs, dev practices)
- Anvil, Foundry, and Cargo installed

## Stories and Specs

### S1a.1 — Register device on-chain

| Step | Spec | Can parallelize |
|------|------|-----------------|
| 1 | V1 — Registry Contract | with V2 |
| 2 | V2 — Device CLI `init` | with V1 |
| 3 | V3 — Attester CLI `register` | after V1 |
| 4 | V4 — E2E Validation | after all |

### S1a.2 — Verify registered device

Specs TBD — write after S1a.1 is implemented.

### S1a.3 — Verify unregistered device

Specs TBD — write after S1a.2 is implemented.

## After each story

1. Run the story's validation
2. `just test` passes (no regressions)

## After all stories

1. Full e2e: init → deploy → register → emit → verify (VERIFIED + UNVERIFIED)
2. Review the PR, merge
3. Return to r2lab with feedback
