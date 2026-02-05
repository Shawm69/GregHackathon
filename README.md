# Quorum Escrow (3-of-5 Verification) — Base Sepolia MVP

Minimal ERC20 escrow where *independent verifiers* attest whether a job is complete.

- **Quorum:** 3-of-5 verifiers
- **Fees:**
  - **Verifier fee:** 1% **per verifier** (5% total)
  - **Protocol/coordinator fee:** 0.5%
  - **Net to worker (on approve):** 94.5%
- **Track:** SmartContract / Most Novel Smart Contract (USDC Hackathon)

## Deployed (Base Sepolia)

- **QuorumEscrow:** `0x67349ed235916c42B97243C08d36De5c539b1C07`
- **MockUSDC (demo token, 6 decimals):** `0x34D85d11739eA4Eba4B96C50B844786890350371`

Explorer: https://sepolia.basescan.org/

## Demo transactions (end-to-end proof)

- `createJob`: `0x5fc65923eab8a21bbf10a0c9a37c21a518f7b9f90e27114ed61dba7e9bdd0c2f`
- `submitWork`: `0x02380583ed998ca6722f39b5a56bec3a33e71f2e92c5fb362671555af6d938bd`
- `attest v1`: `0x34960455b5fcae818a0dbf2f1513ebf54179995d77601ab4d5cd99aee48b5466`
- `attest v2`: `0x8623293be45b8755dbc0689f343316235d0189cb34ee55a14160f33033427405`
- `attest v3`: `0xda25f94d7629a6276ef03d4ead6c8ce9d3dc03598c3e38f918c272cbdd1e74f0`
- `finalize`: `0x0fe71660bd9f957eb7b856299fe620fa7b2a3f263aa7a3b13af09baac692c7f0`

Outcome for a **100 USDC** escrow:
- Worker paid: **94.5 USDC** (`94500000` in 6-decimals)
- Each verifier paid: **1.0 USDC**
- Protocol fee: **0.5 USDC**

## Local dev

From this folder:

```bash
npm i
npm test
npx hardhat run --config hardhat.config.cjs scripts/demo-local.cjs
```

> Note: Hardhat warns about Node v25 in this environment. Tests still run.

## Base Sepolia deploy / demo scripts

Set env vars:

```bash
export BASE_SEPOLIA_RPC_URL=https://base-sepolia-rpc.publicnode.com
export DEPLOYER_PRIVATE_KEY=0xYOUR_KEY
```

Deploy:

```bash
npx hardhat run --config hardhat.config.cjs scripts/deploy-base-sepolia.cjs --network baseSepolia
npx hardhat run --config hardhat.config.cjs scripts/deploy-mockusdc-base-sepolia.cjs --network baseSepolia
```

Run demo (requires deployed addresses):

```bash
export QUORUM_ESCROW=0x...
export USDC=0x...
npx hardhat run --config hardhat.config.cjs scripts/demo-base-sepolia.cjs --network baseSepolia
```

## 1-page viewer (humans)

Open:

- `viewer/index.html`

It reads a `jobId` (bytes32) and shows:
- payer/worker/token
- approvals/rejections
- verifier list + whether each has attested
- expected payouts (verifier/protocol/remainder)

You can host this on GitHub Pages, Netlify, Vercel static, etc.

## Contract interface (MVP)

- `createJob(jobId, worker, token, amount, verifiers[5], verifierFeeBps, protocolFeeBps, protocolFeeRecipient)`
- `submitWork(jobId, workHash)`
- `attest(jobId, approved)`
- `finalize(jobId)` (after 3 approvals OR 3 rejections)

## What’s intentionally NOT in MVP

- verifier staking/slashing
- reputation
- dispute appeals
- partial payouts / milestones
- signature-based offchain attestations

MVP goal: show the **primitive** works.
