# Slice 1 — Horizontal Sub-Slices

Slice 1 stories (S1.1-S1.6) define the full acceptance criteria vertically (one feature each). Implementation is sliced **horizontally** — thin end-to-end passes through all layers.

## Sub-Slice Map

### Slice 1a — "The Wire"

**Goal:** Thinnest possible end-to-end pass. Hardcode everything, prove the chain works.

| Layer | What | Shortcuts |
|-------|------|-----------|
| contracts/ | Minimal registry: `registerDevice(bytes32, address)` + `getDevice(bytes32)` + attester seed in constructor | No events, no dynamic attester management |
| device/ | `device init` prints hardcoded serial + address. `device emit` writes JSON with hardcoded data + fake signature | No crypto, no real serial, no keypair |
| attester/ | `attester register --serial X --address Y` submits tx. `attester verify --file path` checks address on-chain → VERIFIED/UNVERIFIED | No signature verification, only on-chain registration check |

**No `common/` crate.** No secp256k1. No emulation mode. No error handling.

**End-to-end flow:**
```
device init          → prints "Serial: HARDCODED-001, Address: 0xHARDCODED"
forge script deploy  → contract on Anvil with attester seeded
attester register    → registers device on-chain
device emit          → writes reading.json with fake data
attester verify      → reads JSON, checks address on-chain → VERIFIED / UNVERIFIED
```

**Story AC coverage:**

| Story | Covered in 1a | Deferred |
|-------|---------------|----------|
| S1.1 | Partial AC-1 (hardcoded, no real crypto) | AC-2, AC-3, edge cases |
| S1.2 | AC-1, AC-2, AC-3 | Edge cases |
| S1.3 | AC-1 (happy path) | AC-2, AC-3, edge cases |
| S1.4 | — | All (query subsumed by verify in 1a) |
| S1.5 | Partial AC-1 (hardcoded data, fake signature) | AC-2, AC-3, edge cases |
| S1.6 | AC-1 (VERIFIED), AC-2 (UNVERIFIED) | AC-3 (INVALID), edge cases |

**Exit gate:** VERIFIED for registered device, UNVERIFIED for unregistered. Full chain in terminal.

---

### Slice 1b — "Real Crypto"

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

**Story AC coverage:** All primary ACs for S1.1-S1.6.

---

### Slice 1c — "Polish & CI"

**Goal:** Edge cases, CI integration, e2e automation.

**What gets added:**
- Edge cases: filesystem errors, RPC unreachable, malformed JSON, serial unreadable
- `just integration` + `just e2e` scripts
- CI pipeline: activate integration and e2e jobs
- Full demo walkthrough script

**Story AC coverage:** All edge cases. Full Definition of Done for all stories.

**Exit gate:** Slice 1 gate from story map is met.

---

## Stories (Full Acceptance Criteria)

- [S1.1 — Generate Device Identity](s1.1-generate-device-identity.md)
- [S1.2 — Deploy Contract + Seed Attester](s1.2-deploy-contract.md)
- [S1.3 — Register Device via CLI](s1.3-register-device.md)
- [S1.4 — Query Device Status](s1.4-query-device-status.md)
- [S1.5 — Emit Signed Reading](s1.5-emit-signed-reading.md)
- [S1.6 — Verify Device Data](s1.6-verify-device-data.md)
