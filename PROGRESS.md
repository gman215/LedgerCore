# LedgerCore — Progress

> **Update this file at the end of every session.** It is how a new chat (or a new tool) knows where you are.
> Plan: [`STUDY_PLAN.md`](STUDY_PLAN.md) · Resources: [`RESOURCES.md`](RESOURCES.md)

## Current status
- **Week:** 1 — Spring Boot Fundamentals and the Request Lifecycle
- **Current task:** Write `PingController` (`GET /api/ping` → `{"status":"ok"}`) in `com.ledgercore.system`, using a Java `record`.
- **Blockers / open questions:** —
- **Last updated:** 2026-11-11

---

## Week 1 — Spring Boot Fundamentals *(in progress)*
- [x] Environment verified (Java 21, Docker, PostgreSQL 16, Git)
- [x] Spring Boot project generated with Initializr (Boot 4.1.2, webmvc starter only)
- [x] `./mvnw -v` works; app starts; `curl /` returns the default JSON 404
- [x] First commit `773b2c4 Initialize Spring Boot Project`
- [ ] Rename branch `master` → `main` (`git branch -m main`)
- [ ] `PingController` returns 200 + `{"status":"ok"}`
- [ ] Predict the status code a POST to `/api/ping` returns, then verify it
- [ ] Extract `PingService` and use constructor injection
- [ ] `@WebMvcTest` for `/api/ping` (200, JSON, 405 on POST) plus a plain JUnit test for the service
- [ ] Work on a feature branch and self-review it as a PR
- [ ] `./mvnw test` green

**Questions to answer from memory:**
- [ ] What does `@SpringBootApplication` combine? *(reviewed 2026-11-10; re-check)*
- [ ] What does `<parent>` provide, and why do dependencies omit `<version>`? *(reviewed; re-check)*
- [ ] Why use `mvnw` instead of a global Maven install? *(reviewed; re-check)*
- [ ] What is the difference between a 404 and "connection refused"? *(reviewed; re-check)*
- [ ] Why prefer constructor injection?
- [ ] Why does a POST to a GET-only route return 405 and not 404?
- [ ] Explain the request path: Tomcat → DispatcherServlet → controller → Jackson

## Week 2 — SQL, PostgreSQL, Connecting Spring
- [ ] PG Tutorial ch. 2 and §3.3–3.4 read; PGExercises Basic/Joins/Aggregates done
- [ ] Decision written down: native Postgres vs. Docker (and which port)
- [ ] `ledgercore` database and a non-superuser created
- [ ] JPA, PostgreSQL driver, and Flyway dependencies added
- [ ] `V1__create_accounts.sql` applied by Flyway; credentials kept out of git
- [ ] Schema rebuilds from scratch after dropping the database

## Week 3 — Double-Entry Accounting and Schema Design
- [ ] `docs/adr/0001-ledger-model.md` written and reviewed
- [ ] `V2__ledger_schema.sql` with constraints
- [ ] UPDATE/DELETE on entries blocked
- [ ] Constraint tests reject bad rows

## Week 4 — Account Management API
- [ ] `POST /api/accounts`, `GET /api/accounts/{id}`
- [ ] Validation plus `ProblemDetail` errors (400/404/409)
- [ ] Testcontainers integration tests green under `./mvnw verify`

## Week 5 — ACID and Atomic Postings
- [ ] `PostingService` writes balanced journal transactions atomically
- [ ] Deposit / withdraw (no overdraft) / transfer
- [ ] Failure-injection test proves zero partial writes

## Week 6 — Concurrency I
- [ ] A failing concurrency test (50 concurrent withdrawals) proves the race exists
- [ ] Hermitage Postgres anomalies reproduced by hand in `psql`
- [ ] Baseline failure rate recorded

## Week 7 — Concurrency II
- [ ] `docs/adr/0002-concurrency-strategy.md`
- [ ] Locking strategy with deterministic lock ordering
- [ ] Week-6 test passes 20 of 20 runs; total balance unchanged after 200 crossed transfers

## Week 8 — Idempotency
- [ ] `Idempotency-Key` header plus an idempotency table with a UNIQUE constraint
- [ ] Retry replays the stored response; same key with a different body is rejected
- [ ] 10 concurrent requests with the same key produce exactly 1 posting

## Week 9 — Reliability and Reconciliation
- [ ] Reversal entries (never edit existing entries)
- [ ] Reconciliation check: every journal transaction sums to 0; balances equal the sum of entries
- [ ] Crash-mid-transfer and lost-response retry tests pass

## Week 10 — Observability and Performance
- [ ] Actuator, metrics, request-ID logging
- [ ] k6 benchmark (hot account vs. spread) documented in `docs/benchmarks.md`
- [ ] EXPLAIN before and after adding an index

## Week 11 — Hardening and CI
- [ ] GitHub Actions running `./mvnw verify`
- [ ] Self-review checklist applied; fixes merged

## Week 12 — Portfolio
- [ ] README (architecture, how to run, test evidence, benchmarks, limitations)
- [ ] Mock interview passed

---

## Concepts learned (write each in your own words)
| Date | Concept | My one-sentence explanation |
|---|---|---|
| 2026-11-10 | `.gitignore` / build output | Commit sources, not outputs: `target/` is regenerated from source, and committing it causes conflicts, staleness, and bloat. |

## Session log
| Date | What I did | What I'll do next |
|---|---|---|
| 2026-11-10 | Environment check, generated project, ran app, first commit | Ping endpoint |
| 2026-11-11 | Researched resources, created study plan and progress files | Ping endpoint, rename branch to `main` |
