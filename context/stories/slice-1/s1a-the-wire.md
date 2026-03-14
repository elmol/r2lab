# S1a — Verify Device Data Origin (The Wire)

## User Story

As anyone, I can verify whether a data reading comes from a registered device so that I can distinguish trusted from untrusted sources.

## Context

This is the walking skeleton — the thinnest possible end-to-end pass. Everything is hardcoded. No real crypto. The only question this story answers is: **does the chain work?**

## Acceptance Criteria

**AC-1: Data from a registered device is VERIFIED**
- Given a device that has been initialized and registered on-chain by an attester
- When I verify its data reading against the registry
- Then the result is VERIFIED

**AC-2: Data from an unregistered device is UNVERIFIED**
- Given a data reading from a device that is NOT registered on-chain
- When I verify it against the registry
- Then the result is UNVERIFIED

## Preconditions

- A registry contract is deployed with a seeded attester
- A device has emitted a data reading
- The device has been registered on-chain by the attester

## Scope (What "hardcoded" means in The Wire)

- Device identity: fixed serial + fixed Ethereum address (no keypair, no crypto)
- Data reading: fixed temperature, real timestamp, fake signature
- Registration: happy path only (no duplicate check, no unauthorized check)
- Verification: address match only (no signature verification)

## Parent Stories

This story covers partial ACs from the following Slice 1 stories:

| Story | AC covered | What's hardcoded |
|-------|------------|------------------|
| S1.1 — Generate Device Identity | AC-1 partial | No crypto, fixed serial + address |
| S1.2 — Deploy Contract + Seed Attester | AC-1, AC-2, AC-3 | Full coverage |
| S1.3 — Register Device via CLI | AC-1 | Happy path only |
| S1.5 — Emit Signed Reading | AC-1 partial | Hardcoded data, fake signature |
| S1.6 — Verify Device Data | AC-1, AC-2 | Address check only, no signature verification |

S1.4 (Query Device Status) is not covered — query is subsumed by verify in this slice.

## Related ADRs

- ADR-0001: Monorepo flat structure
- ADR-0003: Alloy for Rust-EVM bindings
- ADR-0004: Single smart contract for registry + attestation

## Out of Scope

- Real crypto (secp256k1, ECDSA) → Slice 1b
- Emulation mode → Slice 1b
- Error handling (duplicates, unauthorized, tampered data) → Slice 1b
- Edge cases (filesystem, RPC, malformed JSON) → Slice 1c
- `common/` crate → emerges in Slice 1b
- `attester query` subcommand → Slice 1b

## Definition of Done

- VERIFIED for a registered device's data reading
- UNVERIFIED for an unregistered device's data reading
- Full end-to-end flow runs in a terminal: init → deploy → register → emit → verify
