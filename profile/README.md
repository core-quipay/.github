<div align="center">
  <img src="https://raw.githubusercontent.com/core-quipay/quipay-frontend/main/public/quipay-logo-yellow.png" width="96" height="96" alt="Quipay Logo" />
  <h1>Quipay</h1>
  <p><strong>Real-time payroll streaming on Stellar.</strong></p>

[![License](https://img.shields.io/badge/License-Apache%202.0-facc15?style=flat-square&labelColor=000)](https://github.com/core-quipay/quipay-contracts/blob/main/LICENSE)
[![Built on Stellar](https://img.shields.io/badge/Built%20on-Stellar-facc15?style=flat-square&labelColor=000&logo=stellar&logoColor=white)](https://stellar.org)
[![Soroban](https://img.shields.io/badge/Contracts-Soroban-facc15?style=flat-square&labelColor=000)](https://soroban.stellar.org)

[Contracts](https://github.com/core-quipay/quipay-contracts) · [Backend](https://github.com/core-quipay/quipay-backend) · [Frontend](https://github.com/core-quipay/quipay-frontend)

</div>

---

## What this is

Traditional payroll batches a month of earned labor into a single delayed payment. The worker extends their employer 30 days of interest-free credit, then waits days more for banks to settle — and pays fees for the privilege. For cross-border workers, add wire costs, FX spreads, and the requirement of a bank account.

Quipay reframes salary as a **continuous stream**. An employer funds an on-chain treasury vault and opens a payment stream for each worker: an amount per second, an optional cliff date, a start and end. From that moment the worker's balance accrues **every second**, enforced by a Soroban smart contract — not by a payroll department. The worker can withdraw whatever they've already earned at any moment: mid-month, mid-week, mid-day. The employer can cancel a stream at any time and the contract settles it fairly — earned funds to the worker, the rest back to the treasury.

The result: settlement measured in seconds instead of weeks, fees measured in fractions of a cent, custody held by an auditable contract instead of a bank, and coverage for anyone with a Stellar wallet.

|                | Traditional Payroll   | Quipay          |
| -------------- | --------------------- | --------------- |
| **Settlement** | 30 days               | Per second      |
| **Fees**       | 1–3% + wire fees      | ~$0.001         |
| **Custody**    | Bank holds funds      | On-chain escrow |
| **Withdrawal** | Payday only           | Anytime         |
| **Coverage**   | Bank account required | Stellar wallet  |

### How it works, concretely

1. An employer deposits funds (XLM, USDC, or any Stellar asset) into the **payroll vault** contract, which tracks balances and total outstanding liabilities.
2. The employer registers workers in the **workforce registry** and opens a **payroll stream** per worker — flow rate, cliff, start/end. The vault verifies solvency before a stream can open: you cannot promise money the treasury doesn't hold.
3. From the start time, the worker's withdrawable balance accrues continuously as `elapsed time × flow rate`, gated by the cliff.
4. The worker withdraws — partially or fully — whenever they want. Each payout can mint a verifiable **payroll receipt** for compliance and audit trails.
5. Cancelling a stream triggers a prorated settlement: accrued earnings go to the worker, unaccrued funds return to the treasury.
6. Off-chain, a backend keeps PostgreSQL in sync with chain state via background workers, and pushes live balance updates to the web app over WebSockets — so the UI ticks per second without hammering the RPC.

---

## Repositories

Quipay is split into three independently deployable repositories that integrate over public interfaces (Soroban RPC, REST, WebSockets):

| Repo | Role | Stack |
|---|---|---|
| [**quipay-contracts**](https://github.com/core-quipay/quipay-contracts) | On-chain logic: `payroll_stream`, `payroll_vault`, `workforce_registry`, `payroll_receipt`, `automation_gateway`, `dao_governance` — streaming accrual, treasury solvency, receipts, agent authorization. Includes a fuzzing suite. | Rust, Soroban SDK |
| [**quipay-backend**](https://github.com/core-quipay/quipay-backend) | Off-chain services: REST API, Socket.IO server for live earnings, background workers that reconcile on-chain state with PostgreSQL, Redis-backed rate limiting | Node.js 22, Express, TypeScript, PostgreSQL 16, Drizzle |
| [**quipay-frontend**](https://github.com/core-quipay/quipay-frontend) | The web app: employer dashboard (streams, treasury, workforce), worker dashboard (live balance, withdrawals, stream timeline, PDF payslips), Freighter wallet integration | React 18, TypeScript, Vite, Tailwind CSS |

---

## Architecture

```
┌─────────────────────────────────────────────┐
│         Frontend  (Vite + React 18)         │
│  TypeScript · Tailwind CSS · Freighter SDK  │
└─────────┬──────────────────────┬────────────┘
          │ REST / WebSocket     │ signed txs
┌─────────▼────────────┐  ┌──────▼─────────────────────┐
│  Backend (Express)   │  │  Soroban Contracts (Rust)  │
│  API · Socket.IO     │  │  stream · vault · registry │
│  background workers  │  │  receipt · gateway · dao   │
└─────────┬────────────┘  └──────┬─────────────────────┘
          │                      │
┌─────────▼────────────┐  ┌──────▼─────────────────────┐
│     PostgreSQL       │  │     Stellar Network        │
│  (Drizzle · Redis)   │  │  3–5s finality · ~$0.001   │
└──────────────────────┘  └────────────────────────────┘
```

Writes go straight from the **frontend** to the **contracts** via Freighter-signed transactions — the backend never custodies keys. The **backend** independently indexes chain state into PostgreSQL and streams per-second balance updates to connected clients over Socket.IO. The three repos share no code; each can evolve and deploy on its own.

---

## Getting started

Each repo has full setup docs in its README, but the short version:

```bash
# Contracts — build and test the Soroban workspace
git clone https://github.com/core-quipay/quipay-contracts.git
cd quipay-contracts && cargo test --workspace

# Backend — API + workers (needs PostgreSQL; see README for one-line Docker setup)
git clone https://github.com/core-quipay/quipay-backend.git
cd quipay-backend && npm install && npm run dev

# Frontend — the web app
git clone https://github.com/core-quipay/quipay-frontend.git
cd quipay-frontend && npm install && npm run dev
```

The public Stellar **testnet** RPC (`https://soroban-testnet.stellar.org`) is the default everywhere — no mainnet setup is needed for local development.

---

## Status

Quipay is an active work in progress, built in the open on Stellar testnet. The treasury vault base is complete; streaming, registry, and automation contracts are under active development. **Nothing here has been professionally audited** — do not deploy to mainnet with real funds without your own review. See each repo's `SECURITY.md` for reporting vulnerabilities.

## Contributing

We welcome contributions across the whole stack — contracts, backend, and frontend.

1. Pick a repo and read its `CONTRIBUTING.md`
2. Browse open issues — look for `good first issue`
3. Fork, branch, keep PRs focused, add tests for contract or backend logic changes

All repos share the same [Code of Conduct](https://github.com/core-quipay/quipay-contracts/blob/main/CODE_OF_CONDUCT.md) and are licensed under [Apache 2.0](https://www.apache.org/licenses/LICENSE-2.0).

<div align="center">
  <sub>Built on Stellar · Powered by Soroban</sub>
</div>
