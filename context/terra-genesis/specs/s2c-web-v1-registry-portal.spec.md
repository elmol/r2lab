# S2c-Web-V1 — Registry Web Portal (Retrospective)

> **Status:** Implemented (manually, no handoff)
> **Story:** S2c — Web Portal
> **Type:** Retrospective spec — documents what was built outside the SDD flow

---

## Context

The web portal was added manually during the hackathon (PRs #16 and #20) without going through the spec→handoff flow. This retrospective spec documents what exists for traceability.

---

## What Was Built

### Tech Stack
- React 19.1.1 + Vite 7.1.3 (zero backend)
- ethers.js 6.15.0 for direct EVM interaction
- Custom CSS (dark theme, glassmorphism, responsive)
- No routing library — single page app

### Features

1. **Device Registry Browser** — reads `DeviceRegistered` events, displays device cards with address, serial hash, attestation timestamp, verified badge
2. **Device Registration Form** — attester-only form to register devices on-chain (serialHash + deviceAddr)
3. **Capture Verification** — upload image + metadata.json + capture.json (manifest), verify locally (SHA-256 hash match) and on-chain (`verifyCapture()` view function)
4. **Wallet Integration** — MetaMask connection, chain detection, balance display, attester authorization check
5. **Demo Data** — bundled demo capture files in `web/assets/demo-capture/`

### Architecture
- All contract interaction via ethers.js `BrowserProvider` (injected wallet)
- File hashing via Web Crypto API (`crypto.subtle.digest("SHA-256")`)
- Capture prehash replicates Rust prehash algorithm in JS for Rust↔Solidity compatibility
- On-chain verification calls `verifyCapture()` view function (zero gas)

### File Structure
```
web/
├── src/
│   ├── main.jsx          # React entry point
│   ├── App.jsx           # Single component (1,069 lines)
│   ├── contract.js       # ABI + config
│   └── styles.css        # Custom CSS (512 lines)
├── assets/demo-capture/  # Demo files for verification testing
├── index.html
├── package.json
└── .env.example
```

### Contract ABI Used
- `registerDevice(serialHash, deviceAddr)` — write
- `getDevice(serialHash)` — read
- `verifyCapture(captureHash, v, r, s, scriptHash, binaryHash)` — read
- `ATTESTER()`, `isAttester()`, `approvedScriptHash()`, `approvedBinaryHash()` — read
- Events: `DeviceRegistered`, `EnvironmentHashesUpdated`

---

## Known Issues

1. **Branding:** Header says "HardTrust" — should say "TerraGenesis"
2. **Monolithic component:** All 1,069 lines in a single `App.jsx` — works for hackathon but not maintainable
3. **No tests** — no unit or e2e tests for the web
4. **No vite.config.js** — using Vite defaults
5. **No error retry** — failed contract calls are not retried
6. **No accessibility** — minimal aria labels / semantic HTML
7. **File hashing blocks UI** — could benefit from Web Workers

---

## Environment Config

```
VITE_RPC_URL=http://127.0.0.1:8545
VITE_CONTRACT_ADDRESS=0x...
VITE_EXPECTED_CHAIN_ID=31337
```

---

## Dependencies

```json
{
  "react": "^19.1.1",
  "react-dom": "^19.1.1",
  "ethers": "^6.15.0"
}
```

3 production deps — extremely lightweight.
