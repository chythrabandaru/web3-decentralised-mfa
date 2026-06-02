# Decentralised Multi-Factor Authentication on Web 3.0
[![CI](https://github.com/chythrabandaru/web3-decentralised-mfa/actions/workflows/ci.yml/badge.svg)](https://github.com/chythrabandaru/web3-decentralised-mfa/actions/workflows/ci.yml)

> **Distinction Grade — B.Tech Final Year Project · 500+ simulated user sessions · Eliminates centralised single-point-of-failure risk**

![Solidity](https://img.shields.io/badge/Solidity-Smart%20Contracts-grey?style=flat-square&logo=solidity)
![Ethereum](https://img.shields.io/badge/Ethereum-Blockchain-blue?style=flat-square&logo=ethereum)
![React](https://img.shields.io/badge/React-Frontend-61DAFB?style=flat-square&logo=react)
![ZKP](https://img.shields.io/badge/Zero--Knowledge%20Proofs-Privacy-purple?style=flat-square)
![Grade](https://img.shields.io/badge/Grade-Distinction-gold?style=flat-square)

---

## What It Does

A decentralised multi-factor authentication system built on Ethereum smart contracts and zero-knowledge proofs (ZKPs). Replaces centralised identity providers (Auth0, Okta, traditional PKI) with a trustless, tamper-proof authentication flow — eliminating the single point of failure that makes centralised MFA systems attractive attack targets.

Users authenticate without revealing their credentials to any centralised server. The ZKP proves knowledge of the secret without exposing it.

Awarded **Distinction Grade** in B.Tech final evaluation (2025) for research originality, security architecture quality, and engineering rigour.

---

## The Problem with Centralised MFA

```
Traditional MFA (Centralised):

User → [Auth Request] → Centralised Auth Server ← SINGLE POINT OF FAILURE
                              │
                              ▼
                        User Database ← DATA BREACH RISK
                              │
                              ▼
                         MFA Token → User

Attack surface: Auth server, user DB, token delivery channel
One breach = all users compromised
```

```
This System (Decentralised):

User → [ZKP Auth Request] → Ethereum Smart Contract
                                      │
                              Proof verified on-chain
                              No credentials stored anywhere
                                      │
                              Access granted/denied
                              
No central server. No credential database. No single point of failure.
```

---

## Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    User Interface (React)                │
│                                                         │
│   [Connect Wallet]  [Generate ZKP]  [Authenticate]      │
└──────────────────────────┬──────────────────────────────┘
                           │ Web3.js
                           ▼
┌─────────────────────────────────────────────────────────┐
│                 Ethereum Smart Contracts                 │
│                                                         │
│  IdentityRegistry.sol   AuthenticationManager.sol       │
│  ┌──────────────────┐   ┌──────────────────────────┐   │
│  │ - registerUser() │   │ - verifyProof()           │   │
│  │ - updateKeys()   │   │ - issueSession()          │   │
│  │ - revokeUser()   │   │ - revokeSession()         │   │
│  └──────────────────┘   └──────────────────────────┘   │
└──────────────────────────┬──────────────────────────────┘
                           │
                           ▼
┌─────────────────────────────────────────────────────────┐
│              Zero-Knowledge Proof Layer                 │
│                                                         │
│  Circuit: Proves knowledge of secret without            │
│  revealing the secret itself                            │
│                                                         │
│  Library: snarkjs (Groth16 proving scheme)              │
│  Trusted Setup: Powers of Tau ceremony                  │
└─────────────────────────────────────────────────────────┘
```

---

## How the Authentication Flow Works

1. **Registration** — User registers their identity commitment (hash of secret + salt) on-chain. The secret itself is never stored anywhere.

2. **Authentication request** — User wants to authenticate. The smart contract issues a random challenge.

3. **ZKP generation** — User's browser generates a zero-knowledge proof that they know the secret matching the on-chain commitment, without revealing the secret. This computation happens locally.

4. **On-chain verification** — The proof is submitted to the smart contract. The contract verifies the proof mathematically (Groth16 verification). No trusted third party is involved.

5. **Session issuance** — If the proof is valid, a session token is issued on-chain. The session is tied to the user's Ethereum address.

---

## Security Properties

| Property | Traditional MFA | This System |
|----------|-----------------|-------------|
| Single point of failure | ❌ Yes (auth server) | ✅ No (decentralised) |
| Credential database exposure | ❌ Yes (if breached) | ✅ No (nothing stored) |
| Secret revealed during auth | ❌ Yes (to server) | ✅ No (ZKP) |
| Replay attack resistance | ⚠️ Depends on impl | ✅ Yes (on-chain nonces) |
| Censorship resistance | ❌ Server can block | ✅ Yes (permissionless) |
| Auditability | ⚠️ Depends on logs | ✅ Full on-chain audit trail |

---

## Validation

- **500+ simulated user sessions** — registration, authentication, session revocation, key rotation
- **Security scenarios tested** — replay attacks, man-in-the-middle, credential stuffing, server compromise
- **Gas optimisation** — authentication transaction costs under 50,000 gas
- **Smart contract audit** — internal review for common vulnerabilities (reentrancy, integer overflow, access control)

---

## Quick Start

### Prerequisites

```bash
node >= 16
npm >= 8
Hardhat (Ethereum development environment)
MetaMask browser extension (for frontend testing)
```

### Installation

```bash
git clone https://github.com/chythrabandaru/web3-decentralised-mfa
cd web3-decentralised-mfa

# Install dependencies
npm install

# Compile smart contracts
npx hardhat compile

# Run tests
npx hardhat test

# Deploy to local testnet
npx hardhat node
npx hardhat run scripts/deploy.js --network localhost

# Start frontend
cd frontend && npm install && npm start
```

### Run the Test Suite

```bash
npx hardhat test

# Expected output:
# IdentityRegistry
#   ✓ Should register a new user (245ms)
#   ✓ Should reject duplicate registration (89ms)
#   ✓ Should allow key rotation (312ms)
#
# AuthenticationManager
#   ✓ Should verify valid ZKP and issue session (1823ms)
#   ✓ Should reject invalid ZKP (156ms)
#   ✓ Should reject replayed proof (201ms)
#   ✓ Should revoke session (134ms)
#
# 7 passing (3s)
```

---

## Smart Contract Overview

### `IdentityRegistry.sol`

Manages user identities on-chain. Stores identity commitments (not secrets), supports key rotation and revocation.

### `AuthenticationManager.sol`

Handles the authentication flow. Issues challenges, verifies ZKPs using the on-chain verifier, manages session tokens.

### `Verifier.sol`

Auto-generated Groth16 verifier contract (generated via snarkjs). Verifies zero-knowledge proofs in ~50k gas.

---

## Research Background

This project synthesised findings from 30+ peer-reviewed papers on decentralised authentication, self-sovereign identity (SSI), and zero-knowledge proof systems including:

- Groth16 proving scheme (Groth, 2016)
- Self-sovereign identity (Allen, 2016)
- Decentralised identifier (DID) specifications (W3C)
- zkSNARK-based authentication (various)

Full bibliography in [`docs/bibliography.md`](docs/bibliography.md).

---

## Academic Context

**B.Tech Final Year Project** — Computer Science & Engineering (Cyber Security Specialisation)  
Alliance University, Bangalore | 2025 | **Distinction Grade**

Recognised by faculty and industry reviewers for research originality, security architecture quality, and engineering rigour.

---

## Author

**Chythra Bandaru** — Cybersecurity Professional | Blockchain Security Researcher  
[LinkedIn](https://linkedin.com/in/chythrabandaru) · [GitHub](https://github.com/chythrabandaru)
