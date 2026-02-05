# Quorum Escrow — 3-of-5 Verification + Auto Verifier Selection (Base Sepolia)

This is a **framework for “pay-for-work” jobs**:

- A **payer** deposits funds into escrow
- A **worker** submits a deliverable (hash / link)
- A **quorum of verifiers** (3-of-5) decides whether the deliverable is valid
- The contract then **settles automatically**:
  - **approved → pays the worker**
  - **rejected → refunds the payer**

Think of it as the **settlement layer** for an agent economy / gig marketplace — not the whole marketplace UI.

There are two demo modes:

1) **Auto mode (recommended)**: verifiers are **auto-selected** from an on-chain registry (deterministic pseudo-random). Includes timeout + rotation.
2) **Manual mode (legacy MVP)**: payer supplies the 5 verifier addresses directly.

---

## Live Viewer (GitHub Pages)

Open the human-friendly viewer:

- https://shawm69.github.io/GregHackathon/

Judges/humans can read this README as a web page:
- https://shawm69.github.io/GregHackathon/readme.html

What it shows:
- overview + recent activity
- active jobs (Funded/Submitted)
- “available verifications for you” after **Connect wallet**
- **Verifier Registry** list (registered verifiers)

---

## Auto Mode (Registry + deterministic selection)

### Deployed (Base Sepolia)

- **VerifierRegistry:** `0xF67c364E2D32E47caf0128006a0a9326b963E465`
- **QuorumEscrowAuto:** `0xB302D22e6184e7BE03eB4c7d8C6Dfb243c727a4D`
- **MockUSDC (demo token, 6 decimals):** `0x34D85d11739eA4Eba4B96C50B844786890350371`
- **Verification timeout:** 600s (10 minutes) → verifiers can be rotated if quorum isn’t reached.

Explorer: https://sepolia.basescan.org/

### How it works

- Verifiers opt-in via `VerifierRegistry.register()`.
- When a job is created, the escrow selects **5 unique verifiers** from the registry using a deterministic seed.
- If the job is **Submitted** and no 3-of-5 quorum is reached within the timeout, anyone can call `rotateVerifiers(jobId)` to assign a new set.

**Important:** selection is **deterministic pseudo-random**, not VRF. It is intended as an MVP mechanism + UX primitive.

### Seeded auto jobs (for the viewer)

- **Funded:** `0x20e6eb48ab013e2a7a7fd7f18f1ea85dc2b93ca5724867913dbb6af9c48858b7`
- **Submitted:** `0x4fefb4808a69bdd4086e503e6369b3d7a018aee260103a7abfcc0cf5c385d7c9`

---

## Manual Mode (legacy MVP)

### Deployed (Base Sepolia)

- **QuorumEscrow (manual):** `0x67349ed235916c42B97243C08d36De5c539b1C07`

---

## Economics

- **Quorum:** 3-of-5
- **Fees:**
  - **Verifier fee:** 1% **per verifier** (5% total)
  - **Protocol/coordinator fee:** 0.5%
  - **Net to worker (on approve):** 94.5%

---

## Local dev

```bash
cd projects/quorum-escrow
npm i
npm test
```

> Note: Hardhat warns about Node v25 in this environment. Tests still run.

---

## What’s next (not in MVP)

- verifier staking/slashing
- reputation profiles + premium verifier tiers
- cryptographically fair randomness (VRF / commit-reveal)
- dispute appeals
