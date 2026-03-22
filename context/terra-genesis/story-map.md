# TerraGenesis — Story Map

## Backbone (User Activities)

| Activity | Description |
|----------|-------------|
| Register Device | Attester registers a TerraScope on-chain |
| Capture Data | Device captures microscopy image, hashes, signs |
| Verify Data | Anyone verifies a capture came from a registered device |

## Slice 1 — "The Wire" (inherited from HardTrust)

Walking skeleton with temperature readings. Already implemented and released (v0.1.0).

## Slice 2 — "Capture"

Replace temperature readings with real microscopy captures.

### Slice 2a — Device Capture (MVP)

| Story | Description | Dependencies |
|-------|-------------|--------------|
| S2a.1 | `device capture` — execute external command, hash output, sign, write capture.json | Protocol refactor (generic signing) |

### Slice 2a.4 — Environment Attestation

| Story | Description | Dependencies |
|-------|-------------|--------------|
| S2a.4 | Environment attestation — include script/binary hashes + hw info in capture, attester verifies against release | S2a.1, S2a.2 |

### Slice 2b — On-Chain Verification ~~(was: Attester Verify Capture)~~

Note: Off-chain attester verify already works (S2a.1-V3). Slice 2b now focuses on moving verification on-chain.

| Story | Description | Dependencies |
|-------|-------------|--------------|
| S2b.1 | On-chain verify — view function in HardTrust.sol does ecrecover + device registry check, attester CLI invokes it | S2a.1 |
| S2b.2 | On-chain submit proof — persist capture proof on-chain for proof-of-existence (future) | S2b.1 |

**ADR:** ADR-0010 On-Chain Verification Model (Model B — trustless, ecrecover in contract, no trusted attester) — Proposed

### Key Architecture Decisions (Slice 2b)

- **Model B (Trustless):** Contract does ecrecover, anyone can call, no trusted attester needed
- **Extend HardTrust.sol** (not a new contract) — hackathon simplicity
- **S2b.1 = verify-only (view function, FREE)** — no gas, no storage, no tx
- **S2b.2 = submit + persist (future)** — proof-of-existence, survives device deregistration
- **Requires:** `mapping(address => bool) public registeredDevices` reverse lookup in HardTrust.sol
- **Prehash compatible:** Rust keccak256 prehash is already EVM-compatible for ecrecover

### Slice 2c — Web Portal

| Story | Description | Dependencies |
|-------|-------------|--------------|
| S2c | Web-based registry browser, device registration, and capture verification UI | S2b.1 (on-chain verify) |

Note: S2c was built retrospectively (S2c-Web-V1 manual, S2c-Web-V2 branding via SDD). No formal story file — specs serve as the source of truth.

---

## Current Status: All Slices 2a, 2b, 2c implemented. Documentation phase (S0.22).
