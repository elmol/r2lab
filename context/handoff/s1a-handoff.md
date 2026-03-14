# Handoff: S1a — The Wire

## Structure

S1a has 3 horizontal user stories, each delivering end-to-end value. Specs for each story will be created before handoff to implementation.

## Pre-requisites

- S0.1-S0.5 implemented (repo, CI, AI workflow, ADRs, dev practices)
- Anvil, Foundry, and Cargo installed

## Execution Order

| Step | Story | Delivers |
|------|-------|----------|
| 1 | S1a.1 — Register device on-chain | Device identity exists in the registry |
| 2 | S1a.2 — Verify registered device | VERIFIED — trust confirmed |
| 3 | S1a.3 — Verify unregistered device | UNVERIFIED — contrast proven, Wire gate met |

## After all stories

1. Full end-to-end in one terminal: init → deploy → register → emit → verify (VERIFIED + UNVERIFIED)
2. `just build` and `just test` pass
3. Review the PR
4. Merge
5. Return to r2lab with feedback
