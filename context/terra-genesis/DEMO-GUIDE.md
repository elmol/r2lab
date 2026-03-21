# TerraGenesis Demo Guide

One-liner commands for recording a demo. All use env vars from `.env.demo`.

---

## Step 0 — Load Environment

```bash
source context/terra-genesis/.env.demo
```

> Edit `.env.demo` first: set `TERRA_DIR`, RPi credentials. Fill `DEVICE_ADDR`, `SERIAL`, `CONTRACT_ADDR` as you go and re-source.

---

## Step 1 — RPi: Initialize Device

```bash
sshpass -p "$RPI_PASS" ssh $RPI_USER@$RPI_HOST "device init"
```

> Copy `DEVICE_ADDR` and `SERIAL` into `.env.demo` → `source context/terra-genesis/.env.demo`

---

## Step 2 — Local: Start Anvil (separate terminal)

```bash
source context/terra-genesis/.env.demo && anvil
```

---

## Step 3 — Local: Deploy Contract

```bash
forge script script/Deploy.s.sol --rpc-url $RPC_URL --private-key $ANVIL_KEY --broadcast --root $TERRA_DIR/contracts
```

> Copy `CONTRACT_ADDR` into `.env.demo` → `source context/terra-genesis/.env.demo`

---

## Step 4 — Local: Register Device On-Chain

```bash
HARDTRUST_PRIVATE_KEY=$ANVIL_KEY $TERRA_DIR/target/release/attester register --serial $SERIAL --contract $CONTRACT_ADDR --rpc-url $RPC_URL
```

---

## Step 5 — RPi: Capture

```bash
sshpass -p "$RPI_PASS" ssh $RPI_USER@$RPI_HOST "device capture"
```

> `capture.json` is written to RPi's current directory. Captured files go to `./capture-output/`.

---

## Step 6 — Transfer Files RPi to Local

```bash
mkdir -p ./demo-capture && sshpass -p "$RPI_PASS" scp $RPI_USER@$RPI_HOST:~/capture-output/* ./demo-capture/ && sshpass -p "$RPI_PASS" scp $RPI_USER@$RPI_HOST:~/capture.json ./demo-capture/
```

---

## Step 7 — Local: Verify Capture

```bash
$TERRA_DIR/target/release/attester verify --file ./demo-capture/capture.json --contract $CONTRACT_ADDR --rpc-url $RPC_URL
```

> Expected: **VERIFIED (on-chain)**

---

## Step 8 — (Optional) Set Release Hashes + Re-verify

```bash
HARDTRUST_PRIVATE_KEY=$ANVIL_KEY $TERRA_DIR/target/release/attester set-release-hashes --script-hash $SCRIPT_HASH --binary-hash $BINARY_HASH --contract $CONTRACT_ADDR --rpc-url $RPC_URL
```

```bash
# Re-verify — environment should now show MATCH (on-chain)
$TERRA_DIR/target/release/attester verify --file ./demo-capture/capture.json --contract $CONTRACT_ADDR --rpc-url $RPC_URL
```

---

## Demo Flow

```
source .env.demo              → load config
RPi: device init              → get address + serial → update .env.demo
Local: anvil                  → start testnet
Local: forge deploy           → get contract address → update .env.demo
Local: attester register      → register device on-chain
RPi: device capture           → capture image + sign
scp: RPi → Local              → transfer files
Local: attester verify        → VERIFIED (on-chain)
(opt) attester set-release    → register env hashes
(opt) attester verify         → MATCH (on-chain)
```
