# LedgerCore

A backend financial ledger and transaction-processing system. It is built to learn how real payment infrastructure keeps money correct, especially under concurrency, retries, and failures.

> **Status: early development.** Right now the repository contains only a Spring Boot skeleton. Every feature listed below is **planned** and will be checked off as it is implemented and tested. Progress is tracked in [`PROGRESS.md`](PROGRESS.md).
>
> This is an educational project that uses **simulated money**. It is not a production banking system.

## What it will do

- [ ] Create and manage simulated financial accounts
- [ ] Record every money movement in an **immutable double-entry ledger** (each transaction's entries sum to zero)
- [ ] Deposits, withdrawals, and account-to-account transfers, balanced against offset accounts
- [ ] **Atomic** postings: a transfer either fully commits or leaves no trace
- [ ] Safe **concurrent** transfers with no lost updates, no overdrafts, and no money created or destroyed
- [ ] **Idempotent** requests: retrying with the same `Idempotency-Key` never moves money twice
- [ ] Reversals and reconciliation instead of edits; a complete, auditable history
- [ ] Failure-injection, concurrency, and integration tests against real PostgreSQL
- [ ] Documented benchmarks and engineering tradeoffs

## Design principles

- **Money is integer minor units** (cents, `BIGINT`). No floating point, ever.
- **The database enforces the invariants it can** (constraints, transactions, locks). Application code is not the only safeguard.
- **Ledger entries are append-only.** Mistakes are corrected with reversing entries.
- **Modular monolith first.** No extra services or infrastructure until a measured problem requires them.

## Tech stack

Java 21 · Spring Boot 4 · PostgreSQL 16 · Maven (wrapper) · JUnit Jupiter · Testcontainers · Docker

## Running locally

Prerequisites: JDK 21. Maven is not required, because the project uses the Maven wrapper.

```bash
./mvnw test              # run the tests
./mvnw spring-boot:run   # start the app on http://localhost:8080
```

There are no API endpoints yet.

## Project documents

| File | Purpose |
|---|---|
| [`STUDY_PLAN.md`](STUDY_PLAN.md) | 12-week build roadmap and the concepts each milestone covers |
| [`PROGRESS.md`](PROGRESS.md) | What's done, what's next |
| [`RESOURCES.md`](RESOURCES.md) | Free references used (PostgreSQL docs, Stripe/Modern Treasury engineering articles, CMU 15-445, etc.) |

Architecture notes, design decision records (`docs/adr/`), test evidence, and benchmark results will be added as each milestone is completed.
