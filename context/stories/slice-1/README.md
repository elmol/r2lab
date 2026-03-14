# Slice 1 — CLI/Console (End-to-End Proof)

Slice 1 is implemented as **horizontal sub-slices** — thin end-to-end passes through all layers. Each sub-slice has its own user story and specs. Sub-slicing replaces the original vertical stories (S1.1-S1.6) with flat, independent stories per slice.

## Stories

| Story | Name | Status |
|-------|------|--------|
| [S1a](s1a-the-wire.md) | Verify Device Data Origin (The Wire) | Draft |
| S1b | TBD — Real Crypto | Pending |
| S1c | TBD — Polish & CI | Pending |

## Sub-Slice Map

### S1a — "The Wire"

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

**Exit gate:** VERIFIED for registered device, UNVERIFIED for unregistered. Full chain in terminal.

---

### S1b — "Real Crypto"

**Goal:** Replace all hardcoded values with real behavior. Introduce secp256k1 crypto.

**What gets added:**
- Real secp256k1 key generation, ECDSA signing, signature recovery
- `common/` crate emerges (shared crypto between device/ and attester/)
- `--emulate` flag for serial and temperature
- Existing keys guard
- Real temperature reading + signing
- Real signature verification in `attester verify`
- `attester query` subcommand
- Error handling: duplicate registration, unauthorized caller, tampered data (INVALID)
- Events in contract

---

### S1c — "Polish & CI"

**Goal:** Edge cases, CI integration, e2e automation.

**What gets added:**
- Edge cases: filesystem errors, RPC unreachable, malformed JSON, serial unreadable
- `just integration` + `just e2e` scripts
- CI pipeline: activate integration and e2e jobs
- Full demo walkthrough script

**Exit gate:** Slice 1 gate from story map is met.
