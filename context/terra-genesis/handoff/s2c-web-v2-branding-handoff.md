# Handoff: S2c-Web-V2 — TerraGenesis Branding + Simplification

## Spec

Read `docs/specs/s2c-web-v2-branding.spec.md` for the full specification.

## Goal

Transform the web app from a generic HardTrust developer control panel (11 sections, 1,069 lines) into a **simple, focused TerraGenesis verification app** (~5 sections, ~500 lines).

## Files to Modify

1. `web/src/App.jsx` — main component (bulk of the work)
2. `web/src/styles.css` — color palette update
3. `web/index.html` — title + meta

## Implementation Order

### Step 1: Colors (styles.css)

Replace ALL CSS custom properties in `:root` with:

```css
:root {
  --bg-deep:        #0B1622;
  --bg-surface:     #111D2E;
  --panel:          rgba(11, 22, 34, 0.85);
  --panel-border:   rgba(91, 168, 154, 0.15);
  --accent:         #5BA89A;
  --accent-light:   #7EC4B8;
  --accent-soft:    rgba(91, 168, 154, 0.14);
  --yellow:         #E8D44D;
  --yellow-soft:    rgba(232, 212, 77, 0.12);
  --purple:         #6B3FA0;
  --purple-deep:    #3B1F70;
  --ink:            #E8EDF2;
  --muted:          #8A9BB0;
  --good:           #4CAF50;
  --warn:           #E8D44D;
  --danger:         #E57373;
  --shadow:         0 24px 80px rgba(0, 0, 0, 0.35);
  --glow-teal:      0 0 40px rgba(91, 168, 154, 0.15);
}
```

Update body background gradient:
```css
body {
  background: #0B1622;
  background-image:
    radial-gradient(ellipse at 20% 50%, rgba(91, 168, 154, 0.12) 0%, transparent 60%),
    radial-gradient(ellipse at 80% 20%, rgba(107, 63, 160, 0.08) 0%, transparent 50%),
    radial-gradient(ellipse at 50% 80%, rgba(232, 212, 77, 0.05) 0%, transparent 40%);
}
```

Update button gradients to use `--accent` / `--accent-light` instead of the yellow/orange gradient. Update any hardcoded orange references (`#ff895b`, `rgba(255,137,91,...)`) throughout the CSS.

### Step 2: index.html

```html
<title>TerraGenesis — Provenance for Every Observation</title>
<meta name="description" content="Verify biological data provenance from TerraScope microscopes on-chain.">
```

### Step 3: App.jsx — Delete sections

1. **Delete the `storyPoints` array** (lines ~19-35) and its entire rendering section ("What it is for" panel with story-grid)
2. **Delete the `workflowSteps` array** (lines ~37-62) and its entire rendering section ("How it works" panel with workflow-grid)
3. **Delete the status-grid section** (contract address + chain pills) — move this info to a footer

### Step 4: App.jsx — Rebrand + restructure

**Header/Topbar:**
- Eyebrow: `"HardTrust"` → `"TerraGenesis"`
- H1: `"Registry control room for verified devices."` → `"Provenance for every observation."`

**Hero:**
- Kicker: `"Open microscopy meets on-chain provenance"`
- H2: `"Prove your microscopy data is real."`
- Lead paragraph: `"Each TerraScope microscope has its own cryptographic identity. Captures are hashed, signed, and verifiable on-chain — no intermediaries, no trust required."`
- Keep only 1 stat card: `"Registered TerraScopes"` / device count
- Primary CTA: `"Verify a capture"` → scrolls to `#verify`
- Remove "Refresh registry" as visible CTA (can auto-refresh or be a subtle link)

**Add inline "How it works" (3 steps, single row — NOT cards):**
After hero, add a simple horizontal row:
```
[icon] Capture → [icon] Sign → [icon] Verify
"TerraScope takes    "Device hashes and    "Anyone checks
 a biological image"  signs the capture"    provenance on-chain"
```
Use simple text or CSS-only step indicators. No card panels.

**Reorder: Move capture verify section BEFORE registry.** This is the primary action.

**Verify section:**
- Eyebrow: `"Verify"`
- H2: `"Upload a capture and verify its provenance."`
- Keep the 3 file inputs (image, metadata.json, sign.json) and image preview
- **Replace the 9+ result pills** with:
  1. A **big verdict card** (full width): green background + "VERIFIED — from TerraScope [serial]" or red + "NOT VERIFIED". Use the `--good` / `--danger` colors.
  2. A **summary row** with 3 compact items: "Files ✓ | Hash ✓ | On-chain ✓"
  3. A **collapsible "Details" section** (default collapsed): signer address, script hash, binary hash, per-file hashes. Use a `<details><summary>` or a toggle state.

**Registry section:**
- Eyebrow: `"Registry"`
- H2: `"TerraScope microscopes registered on-chain."`
- Keep device cards but rename labels: "device" → "microscope"

**Register section:**
- Only render if `isAuthorizedAttester` is true
- Collapse behind a button: "Register a TerraScope"
- Move wallet info (address, balance, chain) inside this collapsed section
- Delete the explanatory paragraphs about keccak256 and contract internals

**Footer (new):**
```html
<footer>
  <p>TerraGenesis · A biotexturas project · Built on HardTrust</p>
  <p>Contract: {contractAddress} · Chain: {chainId}</p>
</footer>
```

### Step 5: Replace all remaining text

Search for any remaining "HardTrust", "device" (when meaning microscope), "DePIN", "The Wire" references and replace with TerraGenesis/TerraScope language.

## Do NOT change

- `contract.js` — ABI and config are fine, contract is still called HardTrustRegistry on-chain
- Any JavaScript logic (hashing, verification, wallet connection, contract calls)
- File upload functionality
- The web3 integration pattern

## Verification

After all changes:
1. `cd web && npm run dev` — app starts without errors
2. No "HardTrust" visible anywhere in the UI (only footer footnote)
3. Page has ~5 sections, not 11
4. Verify section is the primary action (before registry)
5. Big verdict card shows VERIFIED/NOT VERIFIED clearly
6. Colors are teal/purple, not orange
7. Page feels simple and focused — a hackathon judge understands it in 60 seconds
