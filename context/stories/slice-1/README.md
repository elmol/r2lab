# Slice 1 — Horizontal Sub-Slices

Slice 1 stories (S1.1-S1.6) define the full acceptance criteria vertically (one feature each). Implementation is sliced **horizontally** — thin end-to-end passes through all layers. Each sub-slice has its own user story and small specs.

## Sub-Slice Map

### Slice 1a — "The Wire"

**Story:** [S1a — Verify Device Data Origin](s1a-the-wire.md)

**Goal:** Thinnest possible end-to-end pass. Hardcode everything, prove the chain works.

**Specs:**

| Spec | Scope |
|------|-------|
| [S1a.1](../../specs/s1a.1-registry-contract.spec.md) | Registry contract + deploy + tests |
| [S1a.2](../../specs/s1a.2-device-init.spec.md) | Device init — hardcoded serial + address |
| [S1a.3](../../specs/s1a.3-device-emit.spec.md) | Device emit — hardcoded reading JSON |
| [S1a.4](../../specs/s1a.4-attester-register.spec.md) | Attester register — on-chain registration |
| [S1a.5](../../specs/s1a.5-attester-verify.spec.md) | Attester verify — VERIFIED / UNVERIFIED |
| [S1a.6](../../specs/s1a.6-workspace-e2e.spec.md) | Workspace config + justfile + e2e validation |

**Parent story AC coverage:**

| Story | Covered in 1a | Deferred |
|-------|---------------|----------|
| S1.1 | Partial AC-1 (hardcoded, no real crypto) | AC-2, AC-3, edge cases |
| S1.2 | AC-1, AC-2, AC-3 | Edge cases |
| S1.3 | AC-1 (happy path) | AC-2, AC-3, edge cases |
| S1.4 | — | All (query subsumed by verify in 1a) |
| S1.5 | Partial AC-1 (hardcoded data, fake signature, real timestamp) | AC-2, AC-3, edge cases |
| S1.6 | AC-1 (VERIFIED), AC-2 (UNVERIFIED) | AC-3 (INVALID), edge cases |

**Exit gate:** VERIFIED for registered device, UNVERIFIED for unregistered. Full chain in terminal.

---

### Slice 1b — "Real Crypto"

**Story:** TBD

**Goal:** Replace all hardcoded values with real behavior. Introduce secp256k1 crypto.

**What gets added:**
- Real secp256k1 key generation, ECDSA signing, signature recovery
- `common/` crate emerges (shared crypto between device/ and attester/)
- `--emulate` flag for serial and temperature
- Existing keys guard (AC-2 of S1.1)
- Real temperature reading + signing
- Real signature verification in `attester verify` (not just address check)
- `attester query` subcommand (S1.4)
- Error handling: duplicate registration, unauthorized caller, tampered data (INVALID)
- Events in contract

**Parent story AC coverage:** All primary ACs for S1.1-S1.6.

---

### Slice 1c — "Polish & CI"

**Story:** TBD

**Goal:** Edge cases, CI integration, e2e automation.

**What gets added:**
- Edge cases: filesystem errors, RPC unreachable, malformed JSON, serial unreadable
- `just integration` + `just e2e` scripts
- CI pipeline: activate integration and e2e jobs
- Full demo walkthrough script

**Parent story AC coverage:** All edge cases. Full Definition of Done for all stories.

**Exit gate:** Slice 1 gate from story map is met.

---

## Stories

### Sub-Slice Stories

- [S1a — Verify Device Data Origin (The Wire)](s1a-the-wire.md)

### Full-Scope Stories (Acceptance Criteria)

- [S1.1 — Generate Device Identity](s1.1-generate-device-identity.md)
- [S1.2 — Deploy Contract + Seed Attester](s1.2-deploy-contract.md)
- [S1.3 — Register Device via CLI](s1.3-register-device.md)
- [S1.4 — Query Device Status](s1.4-query-device-status.md)
- [S1.5 — Emit Signed Reading](s1.5-emit-signed-reading.md)
- [S1.6 — Verify Device Data](s1.6-verify-device-data.md)
