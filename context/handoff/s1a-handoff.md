# Handoff: S1a — The Wire

## Structure

S1a is implemented through 6 small specs, each with its own embedded handoff prompt. Execute them in order.

## Pre-requisites

- S0.1-S0.5 implemented (repo, CI, AI workflow, ADRs, dev practices)
- Anvil, Foundry, and Cargo installed

## Execution Order

| Step | Spec | Handoff |
|------|------|---------|
| 1 | [S1a.1 — Registry Contract](../specs/s1a.1-registry-contract.spec.md) | Copy spec to HardTrust, execute embedded prompt |
| 2 | [S1a.2 — Device Init](../specs/s1a.2-device-init.spec.md) | Copy spec to HardTrust, execute embedded prompt |
| 3 | [S1a.3 — Device Emit](../specs/s1a.3-device-emit.spec.md) | Copy spec to HardTrust, execute embedded prompt |
| 4 | [S1a.4 — Attester Register](../specs/s1a.4-attester-register.spec.md) | Copy spec to HardTrust, execute embedded prompt |
| 5 | [S1a.5 — Attester Verify](../specs/s1a.5-attester-verify.spec.md) | Copy spec to HardTrust, execute embedded prompt |
| 6 | [S1a.6 — Workspace + E2E](../specs/s1a.6-workspace-e2e.spec.md) | Copy spec to HardTrust, execute embedded prompt |

## After each spec

1. Verify the spec's validation section passes
2. `just test` still passes (no regressions)

## After all specs

1. Run the full end-to-end validation from S1a.6
2. Verify VERIFIED for registered device, UNVERIFIED for unregistered
3. `just ci` passes
4. Review the PR
5. Merge
6. Return to r2lab with feedback
