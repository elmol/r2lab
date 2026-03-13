# HardTrust Story Map

## Narrative

A Device Owner initializes their Raspberry Pi, generating a unique identity. They share that identity with an Attester, who physically verifies the hardware and registers it on-chain. The device then emits signed data. Anyone can verify whether the data comes from a legitimate, attested device — or from an unverified source. The contrast between attested and non-attested devices tells the whole story.

## Backbone

```
A1: Initialize Device       A2: Verify & Register       A3: Emit Signed Data       A4: Verify Device & Data
(Device Owner)              (Attester)                   (Device Owner)             (Public / Anyone)
```

---

## Slice 1 — CLI/Console (End-to-End Proof)

**Goal:** Prove the entire flow works using only scripts and CLI. No web portal, no wallet UI. Validates the smart contract, cryptography, and data flow with zero frontend risk.

**Demo:** A terminal session showing: device init, attester registration on-chain via script, signed data emission, and CLI-based verification that distinguishes a registered device from an unregistered one.

### A1: Initialize Device

**S1.1 — Generate device identity**
As a Device Owner, I run an init script on my RPi so that the device generates a unique identity (key pair) tied to its hardware serial.
- Script reads hardware serial from the RPi
- Generates an ed25519 key pair and persists it locally
- Prints serial and public key in a clearly labeled format
- Running the script again detects existing keys and warns before overwriting

**S1.2 — Share device identity with Attester** _(manual step)_
As a Device Owner, I share my device's serial and public key with the Attester so that they can register it.
- The Device Owner copies the output from S1.1 and sends it to the Attester by any means (chat, email, in person)
- No tooling is built for this step in Slice 1

### A2: Verify & Register Device

**S1.3 — Register a device on-chain via CLI**
As an Attester, I register a device on-chain using a CLI script so that the device is recognized as attested in the network.
- CLI script accepts serial + public key as arguments
- Calls the smart contract's registration function, signing with the Attester's private key
- The Attester must be pre-authorized in the contract (seeded during deployment)
- Prints transaction hash and confirmation status
- After success, the device is queryable as "registered" on the contract

### A3: Emit Signed Data

**S1.4 — Emit a single signed reading**
As a Device Owner, I run an emission script so that my RPi sends a signed data reading to the network.
- Script reads CPU temperature and current timestamp
- Signs the data payload with the device's private key
- Submits the signed data to the smart contract or off-chain endpoint
- Prints confirmation that data was emitted

### A4: Verify Device & Data

**S1.5 — Verify an attested device's data via CLI**
As a Public verifier, I run a verification script so that I can check whether a device's emitted data is authentic and the device is attested.
- Script accepts a device serial or public key as input
- Queries the smart contract for registration status
- Retrieves the latest emitted data and verifies the signature against the registered public key
- Prints clear result: "VERIFIED: device is registered, data signature is valid"

**S1.6 — Verify contrast with an unregistered device**
As a Public verifier, I run the verification script against an unregistered device so that I can see the contrast between trusted and untrusted data.
- A second RPi (or simulated device) emits signed data without being registered
- Running the verification script against this device clearly returns "UNVERIFIED"
- The contrast between S1.5 and S1.6 is visible in the terminal output

**Slice 1 gate:** The full flow (init, register, emit, verify) runs end-to-end from the command line. A registered device passes verification. An unregistered device fails verification. No web portal is involved.

---

## Slice 2 — Web Portal (Attester + Public Verification)

**Goal:** Move the Attester registration flow and public verification into a web portal with wallet authentication. Build on top of the contracts and cryptography already proven in Slice 1.

**Demo:** Attester connects wallet to a web portal, registers a device through a form. A public page shows emitted data with visual verified/unverified status badges.

### A2: Verify & Register Device

**S2.1 — Connect wallet to portal**
As an Attester, I connect my wallet to the portal so that I am authenticated and authorized to register devices.
- Portal has a "Connect Wallet" flow
- Connected wallet address is checked against the on-chain list of authorized attesters
- If authorized, the Attester sees the registration interface
- If not authorized, a clear message is shown

**S2.2 — Register a device through the portal**
As an Attester, I register a device through the portal by entering its serial and public key so that I don't need to use CLI scripts.
- Portal provides a form with serial and public key fields
- Input validation: serial format and public key format are checked before submission
- Confirmation screen before signing: shows full data and asks Attester to confirm physical verification
- Wallet transaction is triggered; portal shows pending/confirmed/failed status
- On success, device appears as registered on-chain

**S2.5 — Handle registration failure and retry**
As an Attester, when a registration transaction fails, I see a clear error message and can retry with pre-filled data so that I don't lose time re-entering information.
- If the transaction fails, the portal shows a human-readable error message
- The form retains the entered data
- A "Retry" action re-submits without re-entering data

### A4: Verify Device & Data

**S2.3 — Public verification page with trust badges**
As anyone, I visit the public verification page so that I can see data from devices and their trust status.
- Portal shows a list of devices that have emitted data
- Each device shows: latest data (temperature, timestamp), registration status
- Registered devices show a "Verified" badge; unregistered devices show an "Unverified" badge
- No wallet connection required to view this page

**S2.4 — Live-updating data**
As anyone, I see live-updating data on the verification page so that I can tell the system is active and real.
- Data from emitting devices refreshes automatically
- Timestamp of last emission is visible
- The page does not require manual refresh to see new data

**Slice 2 gate:** The Attester can register devices via the web portal with wallet authentication. The public page shows live data with clear verified/unverified visual distinction. The RPi side (init and emit) remains CLI-based from Slice 1.

---

## Slice 3 — Demo Polish (Side-by-Side Comparison)

**Goal:** Make the demo presentation-ready. Two RPis side by side, one attested and one not, with a portal view that tells the story at a glance from a projector.

**Demo:** Projected portal showing two devices emitting identical data. One is clearly marked as verified with attestation details. The other is clearly marked as unverified. A non-technical viewer understands the difference immediately.

### A3: Emit Signed Data

**S3.4 — Continuous data emission**
As a Device Owner, my RPi continuously emits signed data at a regular interval so that the portal always has fresh data during a demo.
- The emission script runs as a persistent process
- Emits data at a configurable interval (e.g., every 30 seconds)
- Emission continues unattended after startup

### A4: Verify Device & Data

**S3.1 — Side-by-side comparison view**
As anyone viewing the portal, I see a side-by-side comparison of an attested and an unattested device so that the value proposition is immediately obvious.
- Portal places two devices next to each other
- The verified device has a green shield/badge, the unverified has a red/gray badge
- The visual difference is legible at projector distance (3+ meters)
- Both devices show the same type of data (temperature)

**S3.2 — Attestation provenance**
As anyone viewing the portal, I see who attested a device and when so that the trust chain is transparent.
- Verified devices display: "Attested by [address] on [date]"
- Unverified devices display: "No attestation record"
- Visible without extra clicks

**S3.3 — Device detail view**
As anyone viewing the portal, I can expand a device's detail view so that I can see technical details (serial, public key, signature status).
- Each device card has an expandable detail section
- Shows: hardware serial, public key (abbreviated), latest data payload, signature verification result, attestation transaction link
- Surface-level view remains jargon-free; details are for the curious

**Slice 3 gate:** The two-RPi demo runs end-to-end with a polished portal view. A non-technical viewer can immediately identify which device is trusted and why.

---

## Release 2 — Below the Line

Stories that improve the workshop experience and system robustness but are not required for the walking skeleton.

| ID | Story | Rationale |
|----|-------|-----------|
| R2.1 | As an Attester, I register multiple devices in a single batch so that workshop registration takes minutes, not tens of minutes | Addresses attester bottleneck for workshops with 15+ participants |
| R2.2 | As anyone, I see human-readable device names instead of raw public keys so that devices are easy to identify | Generated names like "device-blue-fox-42" from public key hash |
| R2.3 | As an Attester, I revoke a device's registration so that incorrectly registered or compromised devices can be removed | Enables error recovery and ongoing network hygiene |
| R2.4 | As an Attester, I see a dashboard showing how many devices I have registered in this session so that I can track workshop progress | "12/20 devices registered" view, projectable for the class |
| R2.5 | As a Device Owner, I see my device's registration status from the RPi console so that I know when registration is complete without checking the portal | CLI polling command that queries the contract |
| R2.6 | As an Attester, I update a device's registered public key without revoking first so that key regeneration can be recovered quickly | Re-attestation flow for when a student re-runs init |
| R2.7 | As a Device Owner, my init script outputs a QR code so that transferring identity data to the Attester is less error-prone | QR code in console for easier data transfer |

---

## Out of Scope

| Item | Reason |
|------|--------|
| Attester onboarding flow | Attesters are pre-registered; no self-service signup |
| Key rotation | Accepted risk for MVP; regeneration requires re-attestation |
| Multi-chain deployment | Single testnet for MVP |
| Real sensor data | CPU temperature is sufficient; no external sensors |
| Mobile app | Portal is web-only |
| Attester reputation or slashing | Trust model is simple pre-authorization |
| Token economics or rewards | No tokenomics in this layer |
| Remote attestation | Attestation is physical and in-person only |
| TPM / secure enclave | Cost and complexity prohibitive for educational context |
| terra-genesis specific integration | This project is an independent base layer |

---

## Implementation Sequence

```
Slice 1 (CLI):     S1.1 → S1.2 (manual) → S1.3 → S1.4 → S1.5 → S1.6
Slice 2 (Portal):  S2.1 → S2.2 → S2.5 → S2.3 → S2.4
Slice 3 (Polish):  S3.4 → S3.1 → S3.2 → S3.3
Release 2:         Prioritize by workshop scale (R2.1 and R2.4 first for large workshops)
```

## Story Count

| Slice | Stories | Scope |
|-------|---------|-------|
| Slice 1 — CLI | 6 | Full end-to-end proof via console |
| Slice 2 — Portal | 5 | Web UI for attestation and verification |
| Slice 3 — Polish | 4 | Demo-ready presentation view |
| Release 2 | 7 | Workshop UX improvements |
| **Total mapped** | **22** | |

## Success Criteria

The walking skeleton (Slices 1-3) is complete when:
- A Device Owner can initialize a RPi and obtain its identity (serial + public key)
- A pre-registered Attester can certify a device on-chain (CLI or portal)
- A certified device emits signed data
- A non-certified device emits data but fails verification
- Anyone can verify device authenticity (CLI or portal)
- The two-RPi demo (one certified, one not) is executable end-to-end
- The system is deployed and accessible with CI/CD
