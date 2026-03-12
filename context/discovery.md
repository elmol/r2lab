# Discovery — HardTrust

## Problem Statement

There is no base layer for registering and verifying physical hardware devices (Raspberry Pi) in a DePIN network. Without a trust mechanism, there is no way to distinguish data emitted by legitimate, certified hardware from data emitted by unverified sources.

## Context

This project serves as a foundational, abstract base layer. It is independent and reusable. [terra-genesis](https://github.com/biotexturas/terra-genesis) may implement on top of it in the future, but this project stands on its own.

The primary use case context is educational/DIY: workshops, schools, universities — where participants assemble hardware and a coach certifies it on-site.

## Actors

| Actor | Name | Description |
|-------|------|-------------|
| Hardware owner | **Device Owner** | Owns the RPi, initiates the registration process |
| Certifier | **Attester** | Pre-registered in the system. Physically verifies the hardware and signs the registration on-chain |
| Anyone | _(no role)_ | Verification is a public action — anyone can verify device authenticity through the portal |

## Registered Entity

**Device** — A Raspberry Pi with a generated identity consisting of:
- Hardware serial number (read from `/sys/firmware/devicetree/base/serial-number`, immutable, unique per device)
- ed25519 key pair (generated on initialization, public key is the on-chain identity)

## End-to-End Flow

1. **Initialize** — RPi runs an initialization script that generates an ed25519 key pair and displays serial + public key in the console (plain text)
2. **Request registration** — Device Owner shares serial + public key with the Attester (copy/paste from console)
3. **Attest** — Attester enters the portal (authenticated with their wallet), inputs the device data, visually confirms the hardware is real and the serial matches, and signs the attestation transaction on-chain
4. **Emit data** — Registered RPi emits signed data (CPU temperature + timestamp as the minimal viable data point)
5. **Verify** — Anyone visits the portal, sees data from devices, and can verify whether the emitting device is registered and attested in the network

## Demo Scenario

Two Raspberry Pis emitting the same type of data (CPU temperature). One is registered and attested, the other is not. The portal displays data from both — but only the attested device's data can be verified as legitimate. The contrast makes the value proposition immediately visible.

## Technical Constraints

- Smart contracts in Solidity, EVM compatible
- Local development first, then deploy to Sepolia testnet
- Minimal public portal, hosted, with full CI/CD pipeline
- No external hardware dependencies — RPi CPU temperature is sufficient for MVP

## Trust Model (MVP)

Simple and explicit:
- Attesters are pre-registered — there is no flow to become an attester in this version
- Attestation is physical and presential — the attester sees the hardware
- The system trusts the attester because they are pre-authorized
- The attester trusts the device because they verified it in person

## Out of Scope (Future Iterations)

- QR code display in console for easier registration
- Real sensor data instead of CPU temperature
- Remote attestation
- TPM / secure enclave / anti-tampering
- Key rotation
- Attester onboarding flow
- terra-genesis specific implementation

## Open Questions

- Smart contract architecture: single registry contract or modular?
- Portal stack (to be decided in Architecture phase)
- Data emission frequency and storage strategy
