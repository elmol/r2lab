# HardTrust Story Map

## Narrative

A Device Owner initializes their Raspberry Pi, generating a unique cryptographic identity. They share that identity with an Attester, who physically verifies the hardware and registers it on-chain. The device then emits signed data. Anyone can verify whether the data comes from a legitimate, attested device — or from an unverified source. The contrast between attested and non-attested devices tells the whole story.

## Backbone

```
A1: Initialize Device       A2: Verify & Register       A3: Emit Signed Data       A4: Verify Device & Data
(Device Owner)              (Attester)                   (Device Owner)             (Public / Anyone)
```

---

## Slice 1 — CLI/Console (End-to-End Proof)

**Goal:** Prove the entire flow works using only CLI. No web portal, no wallet UI. Validates the smart contract, cryptography, and data flow with zero frontend risk.

**Demo:** A terminal session showing: device init, contract deployment, attester registration on-chain, signed data emission, and CLI-based verification that distinguishes a registered device from an unregistered one.

### Sub-Slice Stories (what gets implemented)

Slice 1 is implemented as **horizontal sub-slices** — thin end-to-end passes through all layers. Each sub-slice has its own user story and specs. See [stories/slice-1/README.md](stories/slice-1/README.md) for specs and AC coverage.

| Sub-Slice | Story | Goal |
|-----------|-------|------|
| **S1a** | As anyone, I can verify whether a data reading comes from a registered device so that I can distinguish trusted from untrusted sources | Hardcoded end-to-end — prove the chain works, no crypto |
| **S1b** | TBD | Replace shortcuts — secp256k1, emulation, error handling, query |
| **S1c** | TBD | Edge cases, integration tests, e2e automation |

### Full-Scope Stories (AC reference)

These define the complete acceptance criteria across all sub-slices. Each sub-slice covers a portion of these ACs incrementally.

| Activity | Story | Description |
|----------|-------|-------------|
| A1: Initialize Device | **S1.1** | As a Device Owner, I run the init command on my RPi so that the device generates a unique identity tied to its hardware serial |
| A2: Verify & Register | **S1.2** | As an Attester, I deploy the registry contract to a local chain with a pre-registered attester so that the system is ready to register devices |
| A2: Verify & Register | **S1.3** | As an Attester, I register a device on-chain using the CLI so that the device is recognized as attested in the network |
| A2: Verify & Register | **S1.4** | As anyone, I query a device's registration status via CLI so that I can check whether it is attested |
| A3: Emit Signed Data | **S1.5** | As a Device Owner, I run the emit command so that my device produces a signed data reading stored locally |
| A4: Verify Device & Data | **S1.6** | As anyone, I verify a signed data file against the on-chain registry so that I can distinguish data from attested vs unattested devices |

_Note: The Device Owner shares serial + Ethereum address with the Attester manually (copy/paste, chat, in person). No tooling is built for this step in Slice 1._

**Slice 1 gate:** The full flow (init, deploy, register, emit, verify) runs end-to-end from the command line. A registered device passes verification. An unregistered device fails verification.

---

## Slice 2 — Web Portal (Attester + Public Verification)

**Goal:** Move the Attester registration flow and public verification into a web portal with wallet authentication. Build on top of the contracts and cryptography already proven in Slice 1.

**Demo:** Attester connects wallet to a web portal, registers a device through a form. A public page shows emitted data with visual verified/unverified status badges.

### A2: Verify & Register Device

**S2.1 — Connect wallet to webapp**
As an Attester, I connect my wallet to the webapp so that I am authenticated and authorized to register devices.

**S2.2 — Register a device through the webapp**
As an Attester, I register a device through the webapp so that I don't need to use CLI.

**S2.5 — Handle registration failure and retry**
As an Attester, when a registration fails, I see a clear error and can retry with pre-filled data so that I don't lose time re-entering information.

### A4: Verify Device & Data

**S2.3 — Public verification page with trust badges**
As anyone, I visit the public verification page so that I can see data from devices and their trust status.

**S2.4 — Live-updating data**
As anyone, I see live-updating data on the verification page so that I can tell the system is active and real.

**Slice 2 gate:** The Attester can register devices via the webapp with wallet authentication. The public page shows live data with clear verified/unverified visual distinction.

---

## Slice 3 — Demo Polish (Side-by-Side Comparison)

**Goal:** Make the demo presentation-ready. Two RPis side by side, one attested and one not, with a webapp view that tells the story at a glance from a projector.

**Demo:** Projected webapp showing two devices emitting identical data. One is clearly marked as verified with attestation details. The other is clearly marked as unverified. A non-technical viewer understands the difference immediately.

### A3: Emit Signed Data

**S3.4 — Continuous data emission**
As a Device Owner, my RPi continuously emits signed data at a regular interval so that the webapp always has fresh data during a demo.

### A4: Verify Device & Data

**S3.1 — Side-by-side comparison view**
As anyone viewing the webapp, I see a side-by-side comparison of an attested and an unattested device so that the value proposition is immediately obvious.

**S3.2 — Attestation provenance**
As anyone viewing the webapp, I see who attested a device and when so that the trust chain is transparent.

**S3.3 — Device detail view**
As anyone viewing the webapp, I can expand a device's detail view so that I can see technical details.

**Slice 3 gate:** The two-RPi demo runs end-to-end with a polished webapp view. A non-technical viewer can immediately identify which device is trusted and why.

---

## Release 2 — Below the Line

Stories that improve the workshop experience and system robustness but are not required for the walking skeleton.

| ID | Story | Rationale |
|----|-------|-----------|
| R2.1 | As an Attester, I register multiple devices in a single batch so that workshop registration takes minutes, not tens of minutes | Addresses attester bottleneck for workshops with 15+ participants |
| R2.2 | As anyone, I see human-readable device names instead of raw addresses so that devices are easy to identify | Generated names from address hash |
| R2.3 | As an Attester, I revoke a device's registration so that incorrectly registered or compromised devices can be removed | Enables error recovery and ongoing network hygiene |
| R2.4 | As an Attester, I see a dashboard showing how many devices I have registered in this session so that I can track workshop progress | Projectable for the class |
| R2.5 | As a Device Owner, I see my device's registration status from the RPi console so that I know when registration is complete without checking the webapp | CLI polling command that queries the contract |
| R2.6 | As an Attester, I update a device's registered address without revoking first so that key regeneration can be recovered quickly | Re-attestation flow for when a student re-runs init |
| R2.7 | As a Device Owner, my init command outputs a QR code so that transferring identity data to the Attester is less error-prone | QR code in console for easier data transfer |

---

## Out of Scope

| Item | Reason |
|------|--------|
| Attester onboarding flow | Attesters are pre-registered; no self-service signup |
| Key rotation | Accepted risk for MVP; regeneration requires re-attestation |
| Multi-chain deployment | Single testnet for MVP |
| Real sensor data | CPU temperature is sufficient; no external sensors |
| Mobile app | Webapp is web-only |
| Attester reputation or slashing | Trust model is simple pre-authorization |
| Token economics or rewards | No tokenomics in this layer |
| Remote attestation | Attestation is physical and in-person only |
| TPM / secure enclave | Cost and complexity prohibitive for educational context |
| terra-genesis specific integration | This project is an independent base layer |

---

## Implementation Sequence

Each slice is implemented as horizontal sub-slices (thin end-to-end passes). Sub-slicing is done incrementally — only the current slice is detailed.

```
Slice 1: S1a (The Wire) → S1b (Real Crypto) → S1c (Polish & CI)
Slice 2: Sub-slicing TBD when we reach it
Slice 3: Sub-slicing TBD when we reach it
Release 2: Prioritize by workshop scale (R2.1 and R2.4 first for large workshops)
```

Sub-slice stories, specs, and AC coverage in `context/stories/slice-1/README.md`.

## Story Count

| Slice | Sub-Slice Stories | Full-Scope Stories | Scope |
|-------|-------------------|-------------------|-------|
| Slice 1 — CLI | 3 (S1a, S1b, S1c) | 6 (AC reference) | Full end-to-end proof via console |
| Slice 2 — Webapp | TBD | 5 | Web UI for attestation and verification |
| Slice 3 — Polish | TBD | 4 | Demo-ready presentation view |
| Release 2 | — | 7 | Workshop UX improvements |

## Success Criteria

The walking skeleton (Slices 1-3) is complete when:
- A Device Owner can initialize a RPi and obtain its identity (serial + Ethereum address)
- A pre-registered Attester can certify a device on-chain (CLI or webapp)
- A certified device emits signed data
- A non-certified device emits data but fails verification
- Anyone can verify device authenticity (CLI or webapp)
- The two-RPi demo (one certified, one not) is executable end-to-end
- The system is deployed and accessible with CI/CD
