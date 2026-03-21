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

### Slice 2b — Attester Verify Capture (future)

| Story | Description | Dependencies |
|-------|-------------|--------------|
| S2b.1 | `attester verify-capture` — verify capture.json against on-chain registry | S2a.1 |

### Slice 2c — On-Chain Proof (future)

| Story | Description | Dependencies |
|-------|-------------|--------------|
| S2c.1 | Record image hash on-chain as proof of capture | S2b.1 |

---

## Current Focus: Slice 2a
