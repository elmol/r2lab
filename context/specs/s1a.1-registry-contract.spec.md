# Spec: S1a.1 — Registry Contract

## Goal

Deploy a minimal Solidity contract that can register devices and look them up. This is the on-chain backbone for The Wire.

## Story Reference

- **S1a** — Verify Device Data Origin (The Wire)

## Related ADRs

- ADR-0001: Monorepo flat structure
- ADR-0003: Alloy for Rust-EVM bindings (attester will consume this ABI)
- ADR-0004: Single smart contract for registry + attestation

## What to Build

### `contracts/src/HardTrustRegistry.sol`

**Storage:**

```solidity
struct Device {
    address deviceAddr;
    address attester;
    uint256 attestedAt;
    bool active;
}

mapping(bytes32 => Device) public devices;       // serialHash => Device
mapping(address => bool) public attesters;        // attester address => authorized
address public owner;
```

**Constructor:**

```solidity
constructor(address _attester)
```

- Sets `msg.sender` as owner
- Registers `_attester` as an authorized attester

**Functions:**

```solidity
function registerDevice(bytes32 serialHash, address deviceAddr) external
```
- Caller must be an authorized attester (`attesters[msg.sender] == true`)
- Stores device record with `msg.sender` as attester, `block.timestamp` as attestedAt, `active = true`
- No duplicate check (deferred to Slice 1b)
- No events

```solidity
function getDevice(bytes32 serialHash) external view returns (address deviceAddr, address attester, uint256 attestedAt, bool active)
```
- Returns device record for the given serial hash
- Returns zero values if not found

```solidity
function isAttester(address addr) external view returns (bool)
```
- Returns whether the address is an authorized attester

**No other functions.** No addAttester, no removeAttester, no events, no owner management.

### `contracts/script/Deploy.s.sol`

- Foundry deploy script using `forge script`
- Deploys HardTrustRegistry with Anvil account #1 as the seeded attester
- Deployer: Anvil account #0 (`0xf39Fd6e51aad88F6F4ce6aB8827279cffFb92266`)
- Attester: Anvil account #1 (`0x70997970C51812dc3A010C7d01b50e0d17dc79C8`)
- Prints the deployed contract address

### `contracts/test/HardTrustRegistry.t.sol`

Foundry tests:
- Contract deploys and owner is set
- Constructor seeds the attester
- `isAttester` returns true for seeded attester, false for random address
- `registerDevice` succeeds when called by authorized attester
- `registerDevice` reverts when called by non-attester
- `getDevice` returns correct data after registration
- `getDevice` returns zero values for unregistered serial hash

## What NOT to Build

- Events
- Duplicate registration check
- Dynamic attester management (addAttester/removeAttester)
- Owner transfer
- Any function not listed above

## Validation

```bash
anvil &
cd contracts && forge build
cd contracts && forge test
cd contracts && forge script script/Deploy.s.sol --rpc-url http://127.0.0.1:8545 --broadcast
```

All tests pass. Deploy prints contract address.

## Handoff Prompt

```
Implement the minimal registry contract for HardTrust (Slice 1a).

Read the spec at docs/specs/s1a.1-registry-contract.spec.md first.

Build:
1. contracts/src/HardTrustRegistry.sol — registry with registerDevice, getDevice, isAttester
2. contracts/script/Deploy.s.sol — deploy script for Anvil
3. contracts/test/HardTrustRegistry.t.sol — Foundry tests

No events, no duplicate check, no dynamic attester management.
Run forge test and verify all tests pass.
```
