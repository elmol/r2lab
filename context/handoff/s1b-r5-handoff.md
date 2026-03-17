# Handoff: S1b-R5 — Rename core/ to protocol/ + Internal Modules

## Context

Read the spec at `docs/specs/s1b-r5-protocol-rename.spec.md` first.

This is a **PURE REFACTOR** — no behavior changes, no new features.

The shared library crate is being renamed from `hardtrust-core` to `hardtrust-protocol` because it defines the protocol both parties (device and attester) agree on: what a Reading is, how it's canonically hashed, and how it's signed/verified. "core" was a generic name that didn't communicate intent.

## Instructions

### TDD: Establish green baseline first

```bash
cargo test --workspace
just e2e-the-wire
```

All tests must pass before you change anything.

### Step 1: Create ADR-0008

Create `docs/adrs/adr-0008-protocol-crate.md`:
- **Decision:** Shared library crate is named `hardtrust-protocol` (directory: `protocol/`)
- **Context:** The crate defines the protocol agreement between device and attester — Reading struct, canonical hashing (reading_prehash), ECDSA sign/verify. "core" is a generic catch-all name.
- **Alternatives rejected:**
  - `hardtrust-core` — too generic, becomes a dumping ground
  - Multiple crates (hardtrust-crypto, hardtrust-domain) — over-engineered; sign/verify tightly coupled to Reading
  - Move sign→device, verify→attester — creates cross-dependency in tests
- **Consequence:** Both device and attester depend on `hardtrust-protocol`. Internal modules organize by concern.

### Step 2: Rename core/ to protocol/

- Rename directory: `core/` → `protocol/`
- `protocol/Cargo.toml`: change `name = "hardtrust-core"` → `name = "hardtrust-protocol"`
- Workspace `Cargo.toml`: update members `"core"` → `"protocol"`
- `device/Cargo.toml`: update dependency path + rename
- `attester/Cargo.toml`: update dependency path + rename
- Find and replace ALL `use hardtrust_core::` → `use hardtrust_protocol::` in device/ and attester/

Run `cargo test --workspace` — must pass.

### Step 3: Split lib.rs into modules

Split `protocol/src/lib.rs` into:

**`protocol/src/domain.rs`** — move `Reading` struct with its serde derives.

**`protocol/src/crypto.rs`** — move `sign_reading()`, `verify_reading()`, `reading_prehash()`, `public_key_to_address()`. Add `use super::domain::Reading;` and `use super::error::ProtocolError;` as needed.

**`protocol/src/error.rs`** — move `CoreError` and rename to `ProtocolError`. Update Display impl messages. Keep `std::error::Error` impl.

**`protocol/src/lib.rs`** — becomes re-exports only:
```rust
pub mod dev_config;

mod crypto;
mod domain;
mod error;

pub use crypto::{public_key_to_address, reading_prehash, sign_reading, verify_reading};
pub use domain::Reading;
pub use error::ProtocolError;
```

**`protocol/src/dev_config.rs`** — unchanged.

### Step 4: Update consumer imports

In `device/src/lib.rs` and `device/src/main.rs`: replace `CoreError` → `ProtocolError`.

In `attester/src/lib.rs` and `attester/src/main.rs`: same if applicable.

### Step 5: Move tests to their modules

Move each test to the module that owns the code it tests:
- Reading serialization tests → `domain.rs`
- sign/verify/prehash tests → `crypto.rs`
- CoreError → ProtocolError tests → `error.rs`

Run after each move to catch issues early.

### Final Validation

```bash
cargo test --workspace           # all tests pass
cargo test -p hardtrust-protocol # protocol crate tests pass
just e2e-the-wire                # The Wire gate: PASSED
```

## Branch

```
refactor/s1b-r5-protocol-rename
```

Base: `main`. Create PR targeting `main` when done.
