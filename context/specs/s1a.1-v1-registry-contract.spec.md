# Spec: S1a.1-V1 — HardTrustRegistry Contract

## Parent Story

S1a.1 — Register a Device and Confirm It On-Chain

## Goal

A minimal Solidity contract that lets an authorized attester register a device and lets anyone query whether a device is registered.

## What to Build

### `contracts/src/HardTrustRegistry.sol`

- Constructor takes one `address _attester` parameter, stores `msg.sender` as owner and `_attester` as authorized attester
- `registerDevice(bytes32 serialHash, address deviceAddr)` — requires `msg.sender == attester`, stores the mapping entry
- `getDevice(bytes32 serialHash) returns (address deviceAddr, address attester, uint256 attestedAt, bool active)` — returns zero values if not found
- `isAttester(address addr) returns (bool)` — returns whether the address is the authorized attester
- No events, no duplicate check, no owner management

### `contracts/test/HardTrustRegistry.t.sol`

- Attester can register a device and `getDevice` returns correct data
- Non-attester cannot register (expect revert)
- Unregistered serial hash returns zero values
- `isAttester` returns true for seeded attester, false for random address

### `contracts/script/Deploy.s.sol`

- Foundry deploy script
- Deployer: Anvil account #0 (`0xf39Fd6e51aad88F6F4ce6aB8827279cffFb92266`)
- Attester: Anvil account #1 (`0x70997970C51812dc3A010C7d01b50e0d17dc79C8`)
- Prints deployed contract address

## What NOT to Build

- No Rust code
- No events, no duplicate check
- No dynamic attester management

## Validation

```bash
cd contracts && forge build && forge test -vvv
anvil &
forge script script/Deploy.s.sol --rpc-url http://127.0.0.1:8545 --broadcast
```

## Handoff Prompt

```
Implement the HardTrustRegistry smart contract for HardTrust.

Read the spec at docs/specs/s1a.1-v1-registry-contract.spec.md first.
The contract goes in the existing contracts/ Foundry project.

After implementation, run `forge build && forge test -vvv` to validate.
```
