# UX Research Report -- DePIN Device Registry

**Research Type:** Heuristic Analysis + Expert Review (Discovery Phase)
**Date:** 2026-03-12
**Scope:** MVP registration flow, workshop context, verification portal
**Methods:** Cognitive walkthrough, persona modeling, failure mode analysis
**Note:** This report is based on expert analysis of the proposed flow. Findings should be validated with real users before finalizing design decisions (see Section 7).

---

## 1. User Personas

### Persona A: Device Owner -- "Lena, the Workshop Participant"

**Demographics and Context**
- Age Range: 17-25 (university student or advanced high school)
- Occupation: Student (computer science, engineering, environmental science, or similar)
- Tech Proficiency: Intermediate. Comfortable using a terminal, but not a daily habit. Has used SSH maybe once or twice. Has never interacted with a blockchain wallet.
- Device Preferences: Laptop (macOS or Linux), smartphone always present

**Behavioral Patterns**
- Attends the workshop out of curiosity or course credit, not deep DePIN conviction
- Follows instructions step by step; deviates when confused and may skip steps
- Will ask the person next to them before asking the instructor
- Expects things to "just work" -- tolerance for debugging is low in a workshop setting
- Copy/paste from terminal is something they can do, but they will make mistakes if the output is cluttered

**Goals**
- Primary: Get my device registered and see it appear on the portal. The reward is visible confirmation.
- Secondary: Understand enough of what happened to explain it later (learning outcome)

**Pain Points**
- Does not know what "attestation" or "DePIN" means at the start -- jargon is a barrier
- Unclear which part of the console output to copy (serial vs. public key vs. both vs. other noise)
- No feedback loop: after sharing data with the Attester, they wait with no visibility into progress
- If something breaks, they do not know if the problem is on their side or the Attester's side

**Quotes (Projected)**
> "I ran the script but there's a bunch of text. Which part do I copy?"
> "I sent it to the teacher... now what? Do I just wait?"
> "It says 'registered' on the portal but I don't really understand what that means technically."

**Research Evidence:** Projected persona based on discovery document analysis, workshop context modeling, and established patterns in educational technology onboarding research.

---

### Persona B: Attester -- "Marco, the Workshop Coach"

**Demographics and Context**
- Age Range: 28-50 (teacher, teaching assistant, or technical mentor)
- Occupation: Educator or technical coach at a university, makerspace, or NGO
- Tech Proficiency: High on hardware and systems, moderate on blockchain. Has a MetaMask wallet but does not use it daily. Understands public/private keys conceptually.
- Device Preferences: Laptop (often shared screen or projector during workshop)

**Behavioral Patterns**
- Managing 10-25 participants simultaneously, attention is fragmented
- Will be receiving serial + public key data from multiple students in rapid succession
- Needs to context-switch between helping students debug and performing attestations
- Is the bottleneck in the entire registration flow -- everything depends on this person
- Likely to batch registrations rather than do them one at a time

**Goals**
- Primary: Register all participant devices accurately and quickly so the workshop can proceed to the data emission demo
- Secondary: Not lose credibility by fumbling with the portal or wallet in front of the class

**Pain Points**
- Receiving data from 15+ students via different channels (chat, email, shouting across the room, paper notes) is chaotic
- Entering serial + public key manually is error-prone, especially under time pressure
- Each attestation requires a wallet signature -- if there are 20 devices, that is 20 separate wallet confirmations
- Wallet UX (MetaMask popups, gas estimation, network switching) is friction they cannot control
- If a transaction fails, they need to identify which student's registration failed and retry
- No dashboard showing "registered 12/20 devices" -- progress tracking is absent

**Quotes (Projected)**
> "I have fifteen students sending me keys on Slack, email, and sticky notes. I can't keep track."
> "MetaMask keeps asking me to confirm -- can't I just batch these?"
> "One transaction failed but I don't know which student it was for."

**Research Evidence:** Projected persona based on discovery document analysis, workshop facilitation patterns, and known blockchain wallet usability research.

---

## 2. Pain Points Analysis

### Critical Friction Points (Severity: High)

**P1: Console Output Parsing**
The RPi init script displays serial + public key in plain text. In a terminal environment, this output will be surrounded by boot messages, system logs, and script output. Students must identify which strings are the serial number and which is the public key.
- Risk: Students copy the wrong string, include extra whitespace or line breaks, or miss characters
- Impact: Failed registration, wasted Attester time debugging bad input
- Observation: ed25519 public keys are 44 characters in base64. Serial numbers are typically 16 hex characters. These look very different to an expert but identical ("random characters") to a novice.

**P2: Data Transfer Between Device Owner and Attester**
Copy/paste from RPi console to Attester has no defined channel. In a workshop, this will happen through:
- Chat messages (Slack, WhatsApp, Discord) -- formatting may mangle the key
- Email -- works but slow
- Verbal dictation -- impossible for a 44-character key
- Showing the screen physically -- Attester would need to retype or photograph
- Each method introduces different error modes.
- Risk: Data corruption, confusion, lost messages in a busy chat channel

**P3: Attester as Single Point of Failure and Bottleneck**
One Attester, 20 devices. Each attestation requires:
1. Receiving the data
2. Entering it in the portal
3. Visually confirming the hardware
4. Signing a wallet transaction
5. Waiting for on-chain confirmation
- Estimated time per attestation: 60-90 seconds minimum (optimistic)
- For 20 devices: 20-30 minutes just for registration, assuming zero errors
- Risk: Workshop stalls waiting for registrations. Students lose engagement.

**P4: Wallet Interaction Fatigue**
Every attestation is a separate on-chain transaction requiring a MetaMask (or equivalent) confirmation popup. For 20 devices:
- 20 popups to review and approve
- Potential gas estimation delays on each
- Network congestion or RPC issues cause silent failures
- MetaMask session timeout may require re-authentication mid-batch
- Risk: Attester frustration, accidental rejection, transaction ordering issues

### Moderate Friction Points (Severity: Medium)

**P5: No Feedback Loop for Device Owner**
After the student shares their serial + public key, they enter a dead zone. They do not know:
- Did the Attester receive the data?
- Is the registration in progress?
- Did it succeed or fail?
- What should they do next?
- Risk: Idle students cause classroom management issues. They may re-run the init script or restart the RPi, potentially regenerating keys.

**P6: "Attestation" Is Alien Vocabulary**
For students and even for some educators, "attestation," "DePIN," "on-chain registration," and "signed data" are unfamiliar terms. The mental model of why this matters is not obvious.
- Risk: Participants go through the motions without understanding, reducing educational value
- Impact: Low retention, inability to explain the concept afterward

**P7: Public Key as Identity Is Fragile**
The ed25519 key pair is generated on initialization. If the student re-runs the init script, a new key pair is generated but the serial stays the same. The old public key is now registered on-chain for a serial that uses a different private key.
- Risk: Data emitted after key regeneration cannot be verified. The device appears broken.
- Recovery: Requires the Attester to revoke and re-register -- adding more friction.

### Lower Friction Points (Severity: Low, but worth noting)

**P8: RPi Boot Failures**
In a workshop with 20 RPis, at least 1-3 will have issues: corrupted SD card, power supply problems, incorrect image. These are hardware issues outside the system's control but they affect the registration experience.

**P9: Testnet Instability**
Sepolia testnet occasionally has congestion or faucet issues. If the Attester's wallet runs out of testnet ETH mid-workshop, registrations halt.

---

## 3. Workshop Context Analysis

### Environmental Factors

**Physical Setup**
- 15-25 participants at tables, each with an RPi, power supply, and personal laptop
- Attester (coach) moves between tables or stands at front
- Projector showing portal or instructions
- Noisy, active environment -- verbal instructions get lost
- WiFi network shared by all RPis and laptops -- bandwidth and reliability vary

**Time Constraints**
- Typical workshop slot: 2-3 hours total
- Registration is not the goal; it is a prerequisite. Students want to get to the "interesting part" (seeing data flow, understanding verification).
- If registration takes more than 20-30 minutes for the group, engagement drops sharply.
- Target: Registration phase should take no more than 15 minutes for 20 devices.

**Group Dynamics**
- Fast students finish setup in 2 minutes, slow students take 10. The spread creates idle time.
- Students who finish first will either help others (good) or get bored and distract (bad).
- The Attester cannot help individuals while also processing registrations -- role conflict.
- Peer assistance is likely and should be designed for, not against.

### Critical Workshop UX Requirements

1. **Instructions must be visual and self-contained.** A printed or projected step-by-step guide that students follow independently, without needing the Attester for basic steps.

2. **The init script output must be unmistakable.** The serial and public key should be visually distinct, labeled clearly, and ideally formatted for easy copy/paste (no surrounding noise).

3. **A defined data transfer channel.** The workshop should mandate a single method (e.g., a shared form, a paste-bin, or a dedicated channel) rather than leaving it ad hoc.

4. **Batch operations for the Attester.** The portal should allow the Attester to queue multiple registrations and confirm them efficiently, not one-by-one.

5. **Progress visibility for everyone.** A projected portal view showing "Device 1: registered, Device 2: pending, Device 3: not started" keeps the group aligned.

---

## 4. Portal UX Considerations

### Verification Portal: What Non-Technical Users Need

The public verification portal must communicate trust status instantly. The audience includes workshop participants, school administrators, visitors, and anyone curious. They will not read documentation.

**Required Visual Signals**

| Element | Certified Device | Non-Certified Device |
|---------|-----------------|---------------------|
| Status badge | Green shield with checkmark, labeled "Verified Hardware" | Red or gray badge, labeled "Unverified" |
| Data display | Identical data format (temperature, timestamp) | Identical data format |
| Trust indicator | "Attested by [Attester name/address] on [date]" | "No attestation record" |
| Data signature | "Signature valid" with subtle checkmark | "Cannot verify signature" or "No attestation" |

**Design Principles for the Portal**

1. **Contrast is the message.** The demo scenario (attested vs. non-attested side by side) is the core value proposition. The visual difference must be obvious at a 3-meter viewing distance (projector in a classroom). Use color, size, and iconography -- not just text.

2. **No jargon on the surface.** The portal should say "Verified Hardware" not "Attested Device." It should say "Data is authentic" not "Signature validated against on-chain public key." Technical details can exist in an expandable section for curious users.

3. **Real-time data matters.** Showing live temperature readings updating every few seconds creates a sense of "aliveness" that makes the demo compelling. A static table of past readings is far less engaging.

4. **The "why should I care" moment.** Somewhere visible (but not intrusive), a one-sentence explanation: "This device has been physically verified by a trusted coach. The data it produces can be trusted." This sentence does more work than a technical diagram.

5. **Device identity should be human-readable.** Showing a 44-character public key as the device name is hostile. Allow device nicknames or generate a human-friendly identifier (e.g., "device-blue-fox-42") that maps to the cryptographic identity under the hood.

### Attester Portal: Registration Interface

1. **Input validation in real-time.** As the Attester types or pastes the serial and public key, validate format immediately. "This doesn't look like a valid serial number" before they try to submit.
2. **Confirmation screen before signing.** Show the full data to be registered, the serial, the public key, and ask the Attester to visually confirm against the physical device. Make this a deliberate step, not a popup.
3. **Transaction status tracking.** After signing, show: "Submitting... Confirmed on-chain (block #12345)." Do not leave the Attester wondering if it worked.
4. **Registration history.** A simple list: "You have registered 12 devices in this session." Clickable to see details.

---

## 5. Failure Scenarios and Recovery

### Scenario F1: Typo or Truncation in Serial/Public Key

**Trigger:** Student copies only part of the key, includes a trailing newline, or transposes characters.
**Current behavior (projected):** Transaction submits with wrong data. Device is registered with incorrect public key. Data emitted by the real device cannot be verified.
**Detection:** May not be caught until the data verification step -- potentially 30+ minutes later.
**Recommended handling:**
- Checksum validation on input: reject obviously malformed serials or keys before submission
- Display a confirmation prompt showing the first and last 6 characters of the key with total length: "Public key: `MCowB...Q8Yxk` (44 chars) -- correct?"
- If the RPi is on the same network, consider a local discovery/verification endpoint that confirms "yes, this serial matches this public key on a device I can reach"

### Scenario F2: Wallet Transaction Fails

**Trigger:** Insufficient gas, network error, user rejects popup, MetaMask session expired.
**Current behavior (projected):** Error message in MetaMask. Portal may or may not show an error.
**Recommended handling:**
- Portal must catch and display transaction failures clearly: "Registration failed: [reason]. Please try again."
- Maintain a retry button that pre-fills the data so the Attester does not need to re-enter
- Track pending vs. confirmed transactions explicitly

### Scenario F3: RPi Does Not Boot or Init Script Fails

**Trigger:** Corrupted SD card, power issue, wrong OS image, network not configured.
**Impact:** Student is stuck at step zero. Cannot participate.
**Recommended handling:**
- Provide 2-3 spare pre-imaged SD cards at each workshop
- Init script should have clear error messages: "ERROR: Cannot read serial number. Is this a Raspberry Pi?"
- A "test mode" in the portal where the coach can register a simulated device so no student is left out

### Scenario F4: Student Regenerates Key Pair

**Trigger:** Student re-runs init script (out of curiosity, by mistake, or because they rebooted).
**Impact:** New key pair generated. Old public key is registered on-chain. New key does not match. Data emitted with new key fails verification.
**Recommended handling:**
- Init script should detect an existing key pair and warn: "Keys already exist. Re-generating will invalidate your registration. Continue? [y/N]"
- Default to preserving existing keys
- If regeneration happens, the portal should allow the Attester to update (re-attest) the device with the new key without revoking first

### Scenario F5: Attester Registers Wrong Device

**Trigger:** In the chaos of 20 students, the Attester registers Student A's serial with Student B's public key.
**Impact:** Both devices appear broken from a verification standpoint.
**Recommended handling:**
- The confirmation step should clearly associate device serial with a student name or table number
- Allow the Attester to revoke and re-register without penalty
- The portal should surface mismatches: "Device registered but no valid signed data received in the last 10 minutes -- possible mismatch?"

### Scenario F6: Network Issues During Workshop

**Trigger:** Shared WiFi drops, RPi loses connectivity, blockchain RPC is unreachable.
**Impact:** Registrations stall, data emission stops, portal shows stale data.
**Recommended handling:**
- Init script should work offline (key generation is local)
- Registration data (serial + key) can be collected offline and submitted when network returns
- Portal should show "last seen" timestamps so users understand data freshness
- Consider a local fallback: if the portal can run on the same LAN, reduce dependency on external services

---

## 6. Recommendations (Prioritized for MVP)

### Must Have -- Essential for a Functional Workshop Demo

| ID | Recommendation | Rationale |
|----|---------------|-----------|
| R1 | **Clear, labeled init script output.** The script should print a visually distinct block: a bordered box with "SERIAL: [value]" and "PUBLIC KEY: [value]" on separate labeled lines. No ambiguity. | Eliminates P1 (console parsing). Costs almost nothing to implement. |
| R2 | **Input validation on the portal.** Validate serial format (16 hex chars) and public key format (base64, 44 chars) before allowing submission. Show inline errors. | Prevents F1 (typo/truncation). Standard form validation. |
| R3 | **Transaction status feedback.** After the Attester signs, show explicit status: pending, confirmed, failed. Include retry with pre-filled data. | Prevents F2 confusion. Essential for Attester confidence. |
| R4 | **Visual differentiation on verification portal.** Green shield for verified, gray/red for unverified. Obvious at projector distance. | This is the entire demo value proposition. Without it, the demo fails. |
| R5 | **Key persistence guard.** Init script checks for existing keys and warns before regenerating. Default: keep existing keys. | Prevents F4, which is unrecoverable without Attester intervention. |
| R6 | **Real-time data display on portal.** Show live temperature updates from registered devices. Auto-refresh or websocket. | Makes the demo compelling. Static data fails to convey "this is live, verified hardware." |

### Should Have -- Significantly Improves Workshop Experience

| ID | Recommendation | Rationale |
|----|---------------|-----------|
| R7 | **Human-readable device names.** Generate a friendly identifier (e.g., "device-red-wolf-07") from the public key hash. Display instead of raw key. | Reduces cognitive load for everyone. Makes the portal usable by non-technical audiences. |
| R8 | **Registration progress dashboard.** The Attester sees a list: "12/20 devices registered." Visible to the class on projector. | Addresses workshop coordination. Keeps the group aligned. |
| R9 | **Structured data submission.** Provide a simple web form or shared document where students paste their serial + key, rather than ad-hoc channels. Consider: init script outputs a URL with serial+key as parameters that students can share. | Addresses P2 (data transfer chaos). Reduces Attester's coordination burden. |
| R10 | **Confirmation step with device identity.** Before signing, the portal shows: "You are about to register device [serial] with key [abbreviated]. The device should be physically in front of you. Confirm?" | Addresses F5 (wrong device). Reinforces the physical verification model. |

### Nice to Have -- Improves Experience but Not Blocking for MVP

| ID | Recommendation | Rationale |
|----|---------------|-----------|
| R11 | **Batch registration support.** Attester can paste multiple serial+key pairs (one per line or CSV) and submit them together, signing once. | Addresses P3 and P4 (bottleneck, wallet fatigue). Significant UX win but requires contract design consideration (multicall). |
| R12 | **QR code output on init script.** Display a QR code in the terminal (using ASCII QR libraries) encoding serial + public key. Student shows QR to Attester who scans it. | Eliminates copy/paste entirely. Already noted as future scope in discovery doc. Should be re-evaluated for MVP if feasible. |
| R13 | **Mismatch detection alerts.** Portal flags devices that are registered but have not emitted valid data within N minutes. | Early warning for F5 (wrong device) and F4 (key regeneration). |
| R14 | **Guided workshop mode on portal.** A step-by-step wizard view: "Step 1: All students run init script. Step 2: Students share data. Step 3: Coach registers devices." With progress indicators. | Full workshop orchestration. High value, medium effort. Better suited for v2. |

---

## 7. Validation Questions -- Assumptions to Test with Real Users

These questions represent the highest-risk assumptions in the current design. Each should be tested before committing to implementation.

### About the Device Owner (Student)

**V1: Can students reliably copy the correct data from the RPi console?**
- Test method: Give 10 students an RPi with the init script. Ask them to send you the serial and public key. Measure error rate.
- Assumption at risk: We assume terminal output is readable and parseable by students. It may not be.

**V2: Do students understand what happened after registration?**
- Test method: After a mock registration, ask students to explain in one sentence what "attestation" means and why it matters.
- Assumption at risk: We assume the educational value is self-evident. Students may complete the flow mechanically without understanding.

**V3: What do students do while waiting for the Attester?**
- Test method: Observe a mock workshop. Note what participants do during idle time between init and registration confirmation.
- Assumption at risk: We assume the wait is acceptable. It may cause disengagement.

### About the Attester (Coach)

**V4: How long does one registration actually take?**
- Test method: Time a coach performing 5 consecutive registrations with real data on the portal prototype. Include wallet interactions.
- Assumption at risk: We estimated 60-90 seconds. If it is 3 minutes, 20 devices means a 60-minute registration phase -- unacceptable.

**V5: Can the Attester manage data from 20 students simultaneously?**
- Test method: Simulate 20 students sending serial+key data over a chat channel. Ask the coach to register them all. Measure errors and time.
- Assumption at risk: We assume ad-hoc data collection works. It may collapse at scale.

**V6: Does the Attester understand the wallet interaction well enough to troubleshoot failures?**
- Test method: Introduce a deliberate transaction failure during a mock registration. Observe if the coach can diagnose and recover.
- Assumption at risk: We assume moderate blockchain literacy. The coach may freeze on wallet errors.

### About the Portal and Verification

**V7: Can a non-technical person understand the verified vs. unverified distinction on the portal?**
- Test method: Show the portal to 5 non-technical people (e.g., school administrators). Ask: "Which device's data would you trust? Why?"
- Assumption at risk: We assume visual badges are sufficient. Users may not understand what "verified" means in this context.

**V8: Does live data create the intended "aha moment"?**
- Test method: Demo the portal (attested vs. non-attested) to a small audience. Measure reaction and comprehension.
- Assumption at risk: We assume the side-by-side comparison is compelling. It may be underwhelming if the visual difference is too subtle.

### About the Overall Flow

**V9: Is copy/paste the right data transfer mechanism, or should we start with QR codes?**
- Test method: A/B test: half the group uses copy/paste, half uses QR codes (if prototyped). Compare error rates and time.
- Assumption at risk: QR is listed as "future scope," but if copy/paste error rates are high, QR may be essential for MVP.

**V10: Does the flow work without reliable internet?**
- Test method: Run the workshop with degraded WiFi (throttled or intermittent). Identify breaking points.
- Assumption at risk: We assume stable connectivity. Workshop venues (schools, makerspaces) often have unreliable networks.

---

## Research Summary

### Top 3 Insights

1. **The Attester is the critical bottleneck.** Every design decision should minimize the Attester's per-device time and cognitive load. If the Attester is overwhelmed, the entire workshop stalls. Batch operations, structured data collection, and progress tracking are not luxuries -- they are essential for workshops beyond 5 participants.

2. **The console-to-portal data transfer is the riskiest step.** Copy/pasting cryptographic keys from a terminal is error-prone even for experienced users. In a noisy workshop with 20 students, errors are near-certain. The init script output format and the data transfer channel are the two most impactful UX decisions in the MVP.

3. **The portal's visual clarity makes or breaks the demo.** The entire value proposition of the system is visible in the side-by-side comparison of attested vs. non-attested devices. If a non-technical viewer cannot immediately see and understand the difference, the product fails to communicate its purpose.

### Recommended Next Steps

1. Prototype the init script output format and test it with 5 students (V1)
2. Time the full registration flow end-to-end with a coach and 5 devices (V4)
3. Create a low-fidelity portal mockup and test the verified/unverified visual distinction with non-technical users (V7)
4. Based on findings, decide whether QR codes should move from "future scope" to MVP (V9)

---

**Researcher:** UX Research Agent
**Date:** 2026-03-12
**Phase:** Discovery (Phase 1)
**Status:** Ready for validation with real users
