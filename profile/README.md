<div align="center">
  <img src="https://raw.githubusercontent.com/core-quipay/quipay-frontend/main/public/quipay-logo-yellow.png" width="96" height="96" alt="Quipay Logo" />
  <h1>Quipay</h1>
  <p><strong>Real-time payroll streaming on Stellar</strong></p>

[![License](https://img.shields.io/badge/License-Apache%202.0-facc15?style=flat-square&labelColor=000)](https://github.com/core-quipay/quipay-contracts/blob/main/LICENSE)
[![Built on Stellar](https://img.shields.io/badge/Built%20on-Stellar-facc15?style=flat-square&labelColor=000&logo=stellar&logoColor=white)](https://stellar.org)
[![Soroban](https://img.shields.io/badge/Contracts-Soroban-facc15?style=flat-square&labelColor=000)](https://soroban.stellar.org)

</div>

---

## What is Quipay?

Quipay is an open-source payroll streaming protocol built on [Stellar](https://stellar.org). Instead of monthly salary cycles, workers earn continuously — every second, in real time, straight to their wallet.

Employers set up a payment stream once. The Soroban smart contract handles the rest: accrual, escrow, and instant withdrawal — no intermediaries, no waiting, no friction.

```
Employer deposits → Stream contract → Worker withdraws anytime
```

|                | Traditional Payroll   | Quipay          |
| -------------- | --------------------- | --------------- |
| **Settlement** | 30 days               | Per second      |
| **Fees**       | 1–3% + wire fees      | ~$0.001         |
| **Custody**    | Bank holds funds      | On-chain escrow |
| **Withdrawal** | Payday only           | Anytime         |
| **Coverage**   | Bank account required | Stellar wallet  |

---

## Repositories

| Repo | Description | Stack |
| --- | --- | --- |
| [**quipay-contracts**](https://github.com/core-quipay/quipay-contracts) | Soroban smart contracts — streaming, escrow, treasury solvency | Rust / Soroban |
| [**quipay-backend**](https://github.com/core-quipay/quipay-backend) | REST API, WebSocket server, and background workers that keep on-chain state in sync | Node.js / Express / TypeScript / PostgreSQL |
| [**quipay-frontend**](https://github.com/core-quipay/quipay-frontend) | Web app for employers and workers | React / TypeScript / Vite |

---

## Architecture

```
┌──────────────┐     ┌──────────────┐     ┌────────────────────┐
│   Frontend   │ ──▶ │   Backend    │ ──▶ │  Soroban Contracts │
│ React + Vite │     │ Express API  │     │  Stellar Network   │
└──────────────┘     │ + workers    │     └────────────────────┘
                     └──────┬───────┘
                            ▼
                     ┌──────────────┐
                     │  PostgreSQL  │
                     └──────────────┘
```

---

## Contributing

We welcome contributions across the whole stack — contracts, backend, and frontend.

1. Pick a repo and read its `CONTRIBUTING.md`
2. Browse the open issues (each repo has `good first issue` candidates)
3. Fork, branch, and open a PR

All repos share the same [Code of Conduct](https://github.com/core-quipay/quipay-contracts/blob/main/CODE_OF_CONDUCT.md) and [Security Policy](https://github.com/core-quipay/quipay-contracts/blob/main/SECURITY.md).

---

## License

All Quipay repositories are licensed under [Apache 2.0](https://www.apache.org/licenses/LICENSE-2.0).

<div align="center">
  <sub>Built on Stellar · Powered by Soroban</sub>
</div>
