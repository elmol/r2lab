# S2c-Web-V2 — TerraGenesis Branding

> **Status:** Draft
> **Story:** S2c — Web Portal
> **Depends on:** S2c-Web-V1 (implemented)

---

## Context

The web app was built with HardTrust branding. It needs to reflect the TerraGenesis / biotexturas identity — connecting biology, open hardware, and on-chain provenance.

### Brand Sources

- **biotexturas.org** — "Connecting people, machines, and ecosystems"
- **Mission:** "Creating a decentralized platform to hybridize research and education by interfacing intelligent machines, people, and ecosystems"
- **Spirit:** "Build it! Grow it! Hack it!" — DIY science, maker culture, open source
- **Logo:** Organic lichen/microbial form in a circle, teal green (#5BA89A) with darker borders (#3D7A6E)
- **TerraScope:** DIY robotic microscope, Raspberry Pi camera, XY+Z stage, captures biological samples

---

## 1. Naming Rules

| Name | Usage | Never |
|------|-------|-------|
| **TerraGenesis** | Product name, web app title, headers | Never say "HardTrust" in UI |
| **TerraScope** | The physical microscope hardware only | Don't use as software name |
| **biotexturas** | Organization, footer attribution | Not in main headers |
| **HardTrust** | Only in "Built on HardTrust" footnote in docs | Never in user-facing UI |
| **Landscapes of Opportunity** | Vision/context sections only | Not in product UI |

---

## 2. Color Palette

Derived from biotexturas logo (teal organic form) + microscopy cluster analysis (purple/yellow/green).

### CSS Custom Properties

```css
:root {
  /* Backgrounds — dark, scientific instrument feel */
  --bg-deep:        #0B1622;        /* deepest background */
  --bg-surface:     #111D2E;        /* card/panel surface */
  --panel:          rgba(11, 22, 34, 0.85);
  --panel-border:   rgba(91, 168, 154, 0.15);

  /* Primary — biotexturas teal (from logo) */
  --accent:         #5BA89A;        /* primary teal */
  --accent-light:   #7EC4B8;        /* hover/highlight */
  --accent-soft:    rgba(91, 168, 154, 0.14);  /* subtle backgrounds */

  /* Secondary — cluster yellow (from microscopy analysis) */
  --yellow:         #E8D44D;        /* CTAs, attention */
  --yellow-soft:    rgba(232, 212, 77, 0.12);

  /* Purple — from cluster analysis visualization */
  --purple:         #6B3FA0;        /* accents, decorative */
  --purple-deep:    #3B1F70;        /* background gradients */

  /* Text */
  --ink:            #E8EDF2;        /* primary text */
  --muted:          #8A9BB0;        /* secondary text */

  /* Semantic */
  --good:           #4CAF50;        /* verified, success */
  --warn:           #E8D44D;        /* warning (reuses yellow) */
  --danger:         #E57373;        /* error, unverified */

  /* Effects */
  --shadow:         0 24px 80px rgba(0, 0, 0, 0.35);
  --glow-teal:      0 0 40px rgba(91, 168, 154, 0.15);
}
```

### Background Gradient

Replace current orange/green radial gradients with:

```css
body {
  background: #0B1622;
  background-image:
    radial-gradient(ellipse at 20% 50%, rgba(91, 168, 154, 0.12) 0%, transparent 60%),
    radial-gradient(ellipse at 80% 20%, rgba(107, 63, 160, 0.08) 0%, transparent 50%),
    radial-gradient(ellipse at 50% 80%, rgba(232, 212, 77, 0.05) 0%, transparent 40%);
}
```

### Button Gradient

```css
.btn-primary {
  background: linear-gradient(135deg, var(--accent), var(--accent-light));
  /* Hover: shift toward yellow */
  background: linear-gradient(135deg, var(--accent-light), var(--yellow));
}
```

---

## 3. Typography

Keep **Space Grotesk** — it's modern, technical, fits the DeSci aesthetic. No change needed.

---

## 4. Copy Replacements

### index.html

| Current | New |
|---------|-----|
| `<title>HardTrust Registry</title>` | `<title>TerraGenesis — Provenance for Every Observation</title>` |
| `meta description: ...browsing registered HardTrust devices...` | `Verify biological data provenance from TerraScope microscopes on-chain.` |

### App.jsx — Topbar

| Current | New |
|---------|-----|
| Eyebrow: `"HardTrust"` | `"TerraGenesis"` |
| H1: `"Registry control room for verified devices."` | `"Provenance for every observation."` |

### App.jsx — Hero

| Element | New copy |
|---------|----------|
| Kicker | `"Open microscopy meets on-chain provenance"` |
| H2 | `"TerraGenesis lets TerraScope microscopes prove where their data comes from."` |
| Lead paragraph | `"Each TerraScope microscope has its own cryptographic identity. When it captures a biological image, the data is hashed, signed by the device, and anchored on-chain. Anyone can verify that a specific microscope produced a specific image at a specific time."` |

### App.jsx — Stat Cards

| Current | New |
|---------|-----|
| "Core promise" / "Verified or unverified" | "Core promise" / "Provenance you can verify" |
| "Starting hardware" / "Raspberry Pi" | "Instrument" / "TerraScope" |
| "Trusted devices now" / count | "Registered microscopes" / count |

### App.jsx — Story Points (3 cards)

**Card 1:**
- Title: `"What TerraGenesis does"`
- Body: `"A provenance layer for biological data — linking microscopy captures from TerraScope instruments to verifiable on-chain records."`

**Card 2:**
- Title: `"What it proves"`
- Body: `"That a microscopy image came from a registered TerraScope, was not tampered with, and was captured at a specific time."`

**Card 3:**
- Title: `"Why it matters"`
- Body: `"Community science needs trusted data. TerraGenesis makes biological observations verifiable, reproducible, and ready for decentralized research."`

### App.jsx — Workflow Steps (4 cards)

**Step 1:** "The microscope generates its identity"
> "Each TerraScope has a Raspberry Pi that generates a secp256k1 keypair and derives an Ethereum address — its own on-chain identity."

**Step 2:** "An attester registers it on-chain"
> "A trusted attester confirms the microscope's identity and registers its serial hash and address in the TerraGenesis registry contract."

**Step 3:** "The microscope captures and signs"
> "When a TerraScope takes a biological image, the capture is hashed, signed by the device, and environment metadata is recorded."

**Step 4:** "Anyone can verify the capture"
> "Given a capture file, anyone can check: was this image from a registered microscope? Has it been altered? Was the software environment intact?"

### App.jsx — Section Headers

| Current | New |
|---------|-----|
| "HardTrust makes device provenance inspectable." | "Every capture, traceable to its source." |
| "Registered devices" | "Registered microscopes" |
| "Every device currently registered in the contract." | "TerraScope microscopes registered on-chain." |
| "Verify my device" | "Register a microscope" |
| "Register a device directly on-chain with the attester wallet." | "Register a TerraScope on-chain with the attester wallet." |
| "Choose your own files and run the MVP verification flow." | "Upload a capture and verify its provenance." |

---

## 5. Images

### TerraScope Photo
- Source: biotexturas.org TerraScope page or `demo-capture/capture.jpg`
- Use in: Hero section background (low opacity) or as a featured image
- The actual microscopy capture from demo-capture/ IS a real TerraScope image — use it

### biotexturas Logo
- The teal organic form (lichen in a circle) — use as favicon and/or footer attribution
- Source: biotexturas.org assets

---

## 6. Favicon

Replace default Vite favicon with biotexturas logo or a simplified "TG" mark in teal.

---

## Acceptance Criteria

- [ ] Zero occurrences of "HardTrust" in user-facing web UI
- [ ] All CSS variables updated to new palette
- [ ] Background gradient uses teal/purple/yellow instead of orange
- [ ] Hero section reflects TerraGenesis/microscopy narrative
- [ ] All 3 story cards rewritten for DeSci context
- [ ] All 4 workflow steps rewritten for microscopy context
- [ ] Section headers updated (microscopes not devices)
- [ ] index.html title and meta updated
- [ ] At least one TerraScope/microscopy image visible in the UI
