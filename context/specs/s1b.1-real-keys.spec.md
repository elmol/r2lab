# Spec: S1b.1 — Real Keys

## Parent Story

S1b — Real Crypto (first story: replace hardcoded identity with a real secp256k1 keypair)

## Goal

`device init` generates a real secp256k1 private key using OS entropy, derives the Ethereum address from the public key, reads the hardware serial from the device tree (with a deterministic emulation fallback for CI and dev machines), persists the private key to `~/.hardtrust/device.key`, and prints the real serial and derived address to stdout.

After this story, the Device Owner has a real cryptographic identity. The printed serial and address are what they share with an Attester for on-chain registration.

A new pure function `public_key_to_address` is added to `types/src/lib.rs`. It is independently unit-testable against known Ethereum key derivation test vectors.

`device emit`, `attester`, and the contract are NOT touched.

## What to Build

### Part 1: `public_key_to_address` in `types/`

#### Updated `types/Cargo.toml`

- Add `k256 = { version = "0.13", features = ["ecdsa"] }`
- Add `sha3 = "0.10"` (for keccak256)
- No other new dependencies

#### Updated `types/src/lib.rs`

Add function:

```rust
pub fn public_key_to_address(pk: &k256::ecdsa::VerifyingKey) -> alloy::primitives::Address
```

Derivation:
1. Encode the public key as uncompressed SEC1 (65 bytes, `0x04` prefix)
2. Drop the `0x04` prefix — use only the 64-byte payload (X and Y coordinates)
3. Compute `keccak256` of those 64 bytes
4. Take the last 20 bytes as the Ethereum address

Return type is `alloy::primitives::Address`. If `alloy` is not yet a dependency of `types/`, use `[u8; 20]` and document the return format. Prefer `alloy::primitives::Address` if it is already present in the workspace.

Unit tests in `types/src/lib.rs`:
- Known keypair → expected address (hardcode a test vector from a standard Ethereum key derivation source, e.g., the Ethereum Yellow Paper or a well-known tool like `cast wallet`)
- The function is pure — no I/O, no randomness

---

### Part 2: `device init` rewrite in `device/`

#### Updated `device/Cargo.toml`

- Add `k256 = { version = "0.13", features = ["ecdsa"] }`
- Add `rand_core = { version = "0.6", features = ["getrandom"] }` (for OS entropy)

#### Updated `device/src/main.rs` — `init` subcommand

**Serial read:**

1. Attempt to read `/sys/firmware/devicetree/base/serial-number` as a UTF-8 string.
2. Strip any trailing null bytes or whitespace.
3. If the file does not exist or cannot be read, fall back to `format!("EMULATED-{}", hostname())` where `hostname()` reads `/etc/hostname` (strip trailing newline) or uses `std::env::var("HOSTNAME")` as a secondary fallback. If both fail, use the literal `"EMULATED-unknown"`.
4. The fallback is intentional and documented. CI always runs in emulation mode. No environment variable is needed to toggle between modes — the file's presence or absence determines the mode automatically.

**Key generation:**

1. Generate a random `k256::ecdsa::SigningKey` using `SigningKey::random(&mut OsRng)`.
2. Derive the `VerifyingKey` from it.
3. Call `public_key_to_address` from `hardtrust-types` to get the Ethereum address.

**Key persistence:**

1. Create `~/.hardtrust/` directory if it does not exist (`fs::create_dir_all`).
2. Key guard: if `~/.hardtrust/device.key` already exists, print the following to stderr and exit with a non-zero code:

   ```
   Error: ~/.hardtrust/device.key already exists. Remove it manually to reinitialize.
   ```

3. Encode the private key as a 64-character lowercase hex string (no `0x` prefix), followed by a newline.
4. Write to `~/.hardtrust/device.key`.
5. Set file permissions to `0600` (owner read/write only). On Linux, use `std::os::unix::fs::PermissionsExt`.

**Output (stdout):**

```
Serial:  <serial>
Address: <0x...>
```

Address must be printed with lowercase hex digits and the `0x` prefix.

---

### Output Files

| File | Change |
|------|--------|
| `types/Cargo.toml` | Add k256, sha3 |
| `types/src/lib.rs` | Add `public_key_to_address` function and unit tests |
| `device/Cargo.toml` | Add k256, rand_core |
| `device/src/main.rs` | Rewrite `init` subcommand — real serial + key gen + address derivation + key persistence |

`dev_config.rs` (`DEV_ADDRESS`, `DEV_SERIAL`) is NOT deleted. The constants remain in place but are no longer referenced by `device init`. They will be cleaned up in S1c.

---

## What NOT to Build

- No key rotation or re-initialization flag (`--force`) — the key guard is a hard stop
- No passphrase or encryption on the key file
- No `device info` subcommand
- No changes to `device emit` — it continues to use hardcoded values from `dev_config`
- No changes to `attester` — register and verify are unchanged
- No changes to the contract
- No changes to CI (that is S1c)
- No removal of `dev_config.rs` constants (cleanup is S1c)
- No `--output` flag or configurable key path

---

## Edge Cases

| Case | Expected behavior |
|------|-------------------|
| `~/.hardtrust/device.key` already exists | Print error to stderr, exit non-zero. No overwrite. |
| `/sys/firmware/devicetree/base/serial-number` does not exist | Fall back to `EMULATED-<hostname>` silently |
| `/etc/hostname` unreadable and `HOSTNAME` env var unset | Use literal `"EMULATED-unknown"` |
| `~/.hardtrust/` directory does not exist | Create it with `create_dir_all` before writing |
| Key file write fails (disk full, permissions) | Propagate error via `.expect()` with a descriptive message |

---

## Dependencies

**Must be done before this story:**
- S1a-refactor (Approved) — `types/src/dev_config.rs` exists, `DEV_ADDRESS`/`DEV_SERIAL` are centralized there

**Stories that depend on this story:**
- S1b.2 (Real Signatures) — `device emit` will sign readings using the private key stored by this story
- S1b.3 (E2E Real Crypto) — the full real-crypto flow gate requires real keys

---

## Validation

```bash
just build
just test                        # unit tests pass, including address derivation test vector
device init                      # prints Serial: ... and Address: 0x...
cat ~/.hardtrust/device.key      # 64-char hex string, newline-terminated
ls -l ~/.hardtrust/device.key    # permissions: -rw-------
device init                      # second run: error printed to stderr, exits non-zero, key unchanged
```

The `just e2e-the-wire` gate must still pass after this story (it does not invoke `device init`).

---

## Notes

- `public_key_to_address` belongs in `types/` because it is a pure crypto utility shared across crates. If a `common/` crate is introduced later, it can be moved there. For now, `types/` is the simplest home and avoids introducing a new crate in this story.
- The emulation fallback (`EMULATED-<hostname>`) is deterministic for a given machine but not stable across machines. This is intentional — CI gets a unique-enough serial without requiring RPi hardware.
- `0600` permissions are enforced programmatically. If the file is created on a filesystem that does not support Unix permissions (e.g., FAT32), the write succeeds but the permission set will silently fail. This is an accepted risk for the MVP.
- Address format: lowercase hex with `0x` prefix, no EIP-55 checksum encoding required at this stage.

---

## Handoff Prompt

```
Add real secp256k1 key generation to the device init command in HardTrust.

Read the spec at docs/specs/s1b.1-real-keys.spec.md first.

S1a is complete and the S1a-refactor is approved. Do not modify device emit, attester, or the contract.

Work in this order:
1. Add public_key_to_address to types/src/lib.rs with a known-vector unit test
2. Update types/Cargo.toml (k256, sha3)
3. Rewrite the init subcommand in device/src/main.rs:
   - Read serial from /sys/firmware/devicetree/base/serial-number, fall back to EMULATED-<hostname>
   - Generate a random secp256k1 key using OsRng
   - Derive the Ethereum address via public_key_to_address
   - Write the private key as 64-char hex to ~/.hardtrust/device.key with 0600 permissions
   - Key guard: if the file already exists, print error to stderr and exit non-zero
   - Print Serial and Address to stdout
4. Update device/Cargo.toml (k256, rand_core)

After implementation, validate with:
  just build
  just test
  device init                    # prints Serial + Address
  device init                    # second run: error, no overwrite
  ls -l ~/.hardtrust/device.key  # -rw-------
  just e2e-the-wire              # must still print: The Wire gate: PASSED

Respect the "What NOT to Build" section strictly.
Branch: feat/s1b.1-real-keys
```
