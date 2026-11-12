# LedgerCore — Study-and-Build Plan

> Track progress in [`PROGRESS.md`](PROGRESS.md). Links for every resource number (#1–#33) are in [`RESOURCES.md`](RESOURCES.md).
> Created 2026-11-11. Baseline: 12 weeks at ~9 hr/week, plus DSA practice tracked separately.

**Method, every week:** Study → **Design** (write a short design note before coding) → Implement → Test → Explain (interview-style Q&A).

**Ordering principle:** transactions → concurrency → idempotency → distributed systems. Don't skip ahead.

---

## Part 1 — Skills Assessment

**Repo state at plan creation:** 1 commit on `master`, generated main class plus the default `contextLoads()` test, no application code.

**Version facts:**
1. **Spring Boot 4.1.2.** Most online tutorials target Boot 3. Expect renamed starters (e.g. `spring-boot-starter-webmvc`), different test annotations, and moved packages. When a tutorial doesn't match your code, check its Boot version first.
2. **JUnit Jupiter 6.0.3**, which Boot 4 ships, not JUnit 5. The programming model (`@Test`, assertions, extensions) is essentially the same.

| Area | Evidence so far | Status |
|---|---|---|
| Java OOP, collections, data structures | Coursework and prior projects | Likely solid; spot-check generics and exceptions |
| HTTP / REST | Knows 4xx vs 5xx; fuzzy on TCP vs HTTP layering | Partial; brief review |
| Git | init/add/commit done correctly | Basics OK; learn branching, PRs, commit hygiene |
| Maven / Spring internals | "idk" on `<parent>` and `mvnw` | To learn (expected at this stage) |
| Threads / synchronization | OS course theory | Verify the Java APIs and how they map to DB locking |
| SQL, PostgreSQL, ACID, isolation | Not yet tested | To learn properly |
| Double-entry, idempotency, Testcontainers, benchmarking | — | To learn |

---

## Part 2 — Weekly Roadmap

If an exam week comes up, pause the plan. Don't compress weeks.

### Week 1 — Spring Boot Fundamentals and the Request Lifecycle
- **Objectives:** Explain the Maven lifecycle, DI and component scanning, and the request path: Tomcat → DispatcherServlet → controller → Jackson.
- **Resources (~2.5 hr):** #1, #3, #32 (Missing Semester Git lecture), first lessons of #2.
- **Implement:** (1) `PingController` (`GET /api/ping`). (2) Extract `PingService`, wired with **constructor injection**. (3) Rename the branch to `main`. (4) Use a feature branch and a self-reviewed PR for every task from now on.
- **Test:** `@WebMvcTest` for `/api/ping` (200, JSON, 405 on POST); a plain JUnit test for `PingService` with no Spring.
- **Questions:** What does `@SpringBootApplication` combine? Why prefer constructor injection over field injection? What does `mvn test` run, and why does it compile first? Why is a POST to a GET-only route a 405 and not a 404?
- **Done when:** `./mvnw test` passes in a run your mentor sees, the PR is merged to `main`, and you can explain the request path without notes.

### Week 2 — SQL, PostgreSQL, and Connecting Spring
- **Objectives:** Write SELECT, JOIN, GROUP BY, and transactions by hand. Understand connection config, profiles, and migrations.
- **Resources (~3 hr):** #9 (ch. 2, §3.3–3.4), #10 (Basic, Joins, Aggregates), #29, #4 (in a scratch project).
- **Design decision:** native Postgres 16 on 5432, or Docker on another port? Write down your reasoning.
- **Implement:** Create a `ledgercore` database and a dedicated non-superuser role. Add the JPA, PostgreSQL driver, and Flyway dependencies. Hand-write `V1__create_accounts.sql` (no Hibernate auto-DDL). Pass credentials through environment variables so they never land in git.
- **Test:** The app starts and Flyway applies V1; `contextLoads()` passes against the real DB; `flyway_schema_history` is visible in `psql`.
- **Questions:** Why use migrations instead of `ddl-auto=update`? What is a connection pool? Why not connect as a superuser? What does `BEGIN … ROLLBACK` demonstrate?
- **Done when:** You can drop the database, restart the app, and get the identical schema back automatically.

### Week 3 — Double-Entry Accounting and Ledger Schema Design
- **Objectives:** Debits and credits, normal balances, journal transactions versus entries, offset accounts for deposits and withdrawals, immutability, and the invariant **Σ(entries per journal transaction) = 0**.
- **Resources (~3 hr):** #17 (I, II), #18 (I, II, V), #19, #11.
- **Design first:** Write `docs/adr/0001-ledger-model.md`. Cover the tables (accounts, journal transactions, entries); which invariants the **database** enforces and which the **application** enforces; stored versus computed balance (or both); amounts as `BIGINT` cents plus a currency code; and how a deposit balances against an external offset account (e.g. `world:cash`). Get it reviewed before writing migrations.
- **Implement:** `V2__ledger_schema.sql` with constraints (positive amounts, foreign keys, NOT NULL, currency CHECK). Block UPDATE and DELETE on entries with privileges and/or a trigger, and justify the choice.
- **Test:** SQL-level checks that a negative amount is rejected, an UPDATE on an entry fails, and an entry pointing at a missing account fails.
- **Questions:** Why does every transaction need at least two entries? Where does money "come from" in a deposit? Stored vs. computed balance: what are the tradeoffs? Why correct with a reversing entry instead of an edit?
- **Done when:** The ADR answers all of the above and the constraints reject bad data in tests.

### Week 4 — Account Management API
- **Objectives:** Entities and repositories, DTOs vs. entities, validation, a consistent error contract, and integration tests against real Postgres.
- **Resources (~2.5 hr):** #2 (data and testing lessons), #7, #28, #6.
- **Implement:** `POST /api/accounts` and `GET /api/accounts/{id}`; validation; `ProblemDetail` errors (400/404/409). Justify UUID vs. BIGINT keys.
- **Test:** Testcontainers integration tests (create, 404 on an unknown id, validation failures, duplicates), plus a `@WebMvcTest` for error mapping.
- **Questions:** Why not expose JPA entities directly in the API? Why test against real Postgres instead of H2? When is each of 400, 409, and 422 appropriate?
- **Done when:** `./mvnw verify` runs the Testcontainers tests successfully from a clean checkout.

### Week 5 — ACID Transactions and Atomic Postings
- **Objectives:** What ACID actually guarantees; how `@Transactional` works (proxies, rollback rules, the self-invocation pitfall).
- **Resources (~3 hr):** #5, #18 (IV), #13 (Concurrency Control Theory).
- **Design question first:** What happens if the server crashes after the debit entry is inserted but before the credit?
- **Implement:** A `PostingService` that writes a balanced journal transaction in **one** DB transaction and validates the balance invariant before commit. Build deposits, withdrawals (no overdraft), and transfers on top of it.
- **Test (failure injection):** Throw between the two entry inserts and assert **zero** rows persisted. Show that a checked exception is *not* rolled back by default, then fix it with a rollback rule. Show that an overdrawing withdrawal is rejected with nothing written.
- **Questions:** Which ACID letter prevents a partial transfer? Why doesn't `@Transactional` work on private methods or self-calls? What is the database's actual unit of atomicity?
- **Done when:** The failure-injection tests pass and you can explain why in terms of Postgres transactions, not "Spring handles it."

### Week 6 — Concurrency I: Race Conditions and Isolation Levels
- **Objectives:** Lost updates, check-then-act races, Read Committed vs. Repeatable Read vs. Serializable in Postgres, and Java's `ExecutorService`, `CountDownLatch`, and atomics.
- **Resources (~3 hr):** #8, #12 (§13.2), #14 (reproduce the anomalies by hand in two `psql` sessions).
- **Implement:** **Write the failing test first:** 50 threads each withdraw $10 from a $100 account at once. Under naive code it should show an overdraft or lost updates. Don't fix it yet.
- **Test:** Run it 20 times and record how often it fails. That rate is your baseline.
- **Questions:** Is Read Committed enough for check-balance-then-debit, and why not? What does Postgres do at Serializable when two transactions conflict? Why can a concurrency bug pass 99 runs and fail on the 100th?
- **Done when:** You have a test that fails reliably and can explain the interleaving that causes it, step by step.

### Week 7 — Concurrency II: Locking and Deadlocks
- **Objectives:** Pessimistic locking (`SELECT … FOR UPDATE`) vs. optimistic locking (a version column) vs. Serializable with retry; deadlocks and consistent lock ordering.
- **Resources (~3 hr):** #12 (§13.3), #15, #13 (Two-Phase Locking; MVCC optional), #18 (VI).
- **Design:** `docs/adr/0002-concurrency-strategy.md` comparing at least two of the options.
- **Implement:** Make the Week 6 test pass. Lock accounts in a **deterministic order** (e.g. by id).
- **Test:** The Week 6 test passes 20 of 20 runs. Run 200 concurrent A→B and B→A transfers: no deadlocks, or any deadlock is handled, and Σ of all balances is unchanged.
- **Questions:** Why does id ordering prevent deadlock? When is optimistic locking the better choice? What does Postgres do when it detects a deadlock?
- **Done when:** All concurrency tests pass repeatedly and you can draw the deadlock your lock ordering prevents.

### Week 8 — Idempotency
- **Objectives:** At-least-once delivery, idempotency keys, and why a timeout is ambiguous.
- **Resources (~3 hr):** #21, #22, #24.
- **Design question first:** A client sends a transfer, times out, and retries. List every state the first request could be in.
- **Implement:** An `Idempotency-Key` header on mutating endpoints; an idempotency table with a UNIQUE constraint; replay of the stored response on retry; same key with a different body → 422 (or 409, justify it); handling for requests still in flight.
- **Test:** The same request twice produces one effect and an identical response. Ten threads sending the same key produce exactly one journal transaction. The same key with a different body is rejected.
- **Questions:** Why must the idempotency record commit in the *same* transaction as the posting? Why is "check if the key exists, then insert" unsafe? How long should keys be retained?
- **Done when:** The concurrent duplicate test passes and you can explain why the UNIQUE constraint, not Java code, is the actual guarantee.

### Week 9 — Reliability, Failure Recovery, and Reconciliation
- **Objectives:** Retries with backoff and jitter, failure before vs. after commit, reversal entries, reconciliation.
- **Resources (~3 hr):** #25, #26 (selected lectures only), #23, #20.
- **Implement:** Reversal endpoints that post compensating entries and never edit existing ones. A reconciliation check: every journal transaction sums to 0, and every stored balance equals the sum of its entries. Paginated transaction history.
- **Test:** Kill the app or the DB connection mid-transfer; on restart the ledger is consistent. Simulate a commit that succeeds but whose response is lost, then retry with the same key. Reconciliation passes after the full concurrency suite.
- **Questions:** Why doesn't an HTTP timeout mean the transaction failed? What is the Two Generals problem, and what does it mean for a payments API? Why do you need reconciliation if you already have DB constraints?
- **Done when:** Reconciliation reports zero discrepancies after every suite.

### Week 10 — Observability and Performance
- **Objectives:** Structured logs with correlation ids, metrics, load testing, reading EXPLAIN ANALYZE.
- **Resources (~3 hr):** #31, #30, #16 (selected chapters).
- **Implement:** Actuator health and metrics; a counter and latency timer for transfers; request-id logging. A k6 script for a hot account (contention) and for spread accounts. EXPLAIN on the history query, add an index, then measure again.
- **Test:** Record throughput, p50/p95/p99 latency, and error rate in `docs/benchmarks.md`, along with **hardware, settings, and the exact command**.
- **Questions:** Why is a single hot account a bottleneck under pessimistic locking? Why report p99 and not only the average? Did the index help, and how do you know?
- **Done when:** The benchmarks are reproducible from the documented command and every claim is backed by a measurement.

### Week 11 — Hardening, Refactoring, CI
- **Resources (~1.5 hr):** #32 (Pro Git ch. 3), GitHub Actions docs.
- **Implement:** A GitHub Actions workflow running `./mvnw verify` (Testcontainers included; free for public repos). Self-review against a checklist (naming, transaction boundaries, error handling, test gaps) and fix what you find.
- **Done when:** CI is green on `main` and every merged PR passed CI.

### Week 12 — Portfolio and Interview Readiness
- **Implement:** README (what it is, architecture diagram, how to run it, test evidence, benchmark table, **known limitations**), polished ADRs, a mock interview. An optional demo frontend comes only after this and is clearly secondary.
- **Done when:** You can give a 5-minute verbal walkthrough of the transfer path (HTTP → idempotency → locks → commit) and handle follow-up questions without notes.

---

## Part 3 — Weekly Routine (~9 hr + DSA)

| Day | Block | Focus |
|---|---|---|
| Mon | 1.5 hr | **Study**: only the assigned sections; notes in your own words |
| Tue | 30 min | **Design**: design note or ADR before any code |
| Wed | 2 hr | **Implement** |
| Thu | — | Off (classes) |
| Fri | 2 hr | **Implement and test** |
| Sat | 2.5 hr | **Test and debug**: failure and concurrency tests; open the PR |
| Sun | 30 min | **Recall**: answer the week's questions from memory, rebuild one small piece blind, mentor review; update `PROGRESS.md` |

The split comes to roughly 20% study, 55% coding, 18% testing, and 7% review.
**DSA is separate:** 2–3 hr/week on Grind 75 (#33). LedgerCore does not prepare you for the coding round.

---

## Part 4 — Rules Against Tutorial Hell and AI Dependency

### The stuck ladder
| Time stuck | Action |
|---|---|
| 0–20 min | Read the *entire* error (the last `Caused by:` in the stack trace). Reproduce it reliably. Check the official docs. |
| 20–45 min | Shrink the problem: a smaller test, logging, the debugger, try it directly in `psql`. Write down **expected vs. actual**. |
| 45 min | Ask the CLI for a **hint (Level 2)**. Bring your expected-vs-actual note. |
| +30 min | Ask for **pseudocode (Level 3)**. |
| Still blocked | Ask for **example code (Level 4–5)**, then close it and rewrite it from memory. |

### Rules
- **Ask for example code** only once you can state exactly what you don't understand.
- **Never commit code you can't explain line by line**, including code from the CLI.
- **Watch a tutorial** only for a concept the docs failed to explain, never to copy the current feature.
- **Move on** only when the completion criteria are met, the tests pass in a run your mentor has seen, and you can answer the questions from memory.
- **Productive struggle** means you rule out at least one hypothesis every 15 minutes. If you are rerunning the same thing and hoping, go up one rung on the ladder.
- **Weekly blind rebuild:** on Sunday, rewrite one small piece from the week (a controller, a query, a test) without looking.

---

## Part 5 — Milestone Completion Tests

| Phase | Objective evidence |
|---|---|
| Fundamentals (W1–2) | `./mvnw test` green; schema rebuilds from migrations; request lifecycle explained unaided |
| Accounting design (W3) | ADR reviewed; constraint tests reject invalid rows |
| Accounts (W4) | Testcontainers integration suite green; error contract documented |
| Transactions (W5) | Failure injection proves **zero** partial writes |
| Concurrency (W6–7) | The test that failed before the fix now passes 20/20; Σ balances conserved after 200 crossed transfers |
| Idempotency (W8) | 10 concurrent same-key requests produce exactly 1 posting |
| Reliability (W9) | Reconciliation finds zero discrepancies after crash and retry tests |
| Performance (W10) | Reproducible benchmark document with hardware details and exact commands |
| Portfolio (W11–12) | Green CI; README with limitations; mock interview passed |

---

## Part 6 — Fintech Internship Relevance

**What current postings say** (Summer 2027 listings found 2026-11-11; requirements vary by location, so always check the official posting):
- **Bloomberg:** pursuing a CS degree; C, C++, Python, or Java; deep understanding of data structures and algorithms. (Taken from job-board copies.)
- **Capital One** ([posting](https://www.capitalonecareers.com/job/mclean/technology-internship-program-summer-2027/31238/98697185120)): 10-week program; listings mention Java, Python, and Go, plus AI-tool experience "beyond basic code completion."
- **Wells Fargo** ([posting](https://www.wellsfargojobs.com/en/jobs/r-574285/2027-technology-summer-internship-early-careers-software-engineering/)): algorithms, data structures, API development; a technology skills assessment; some listings require exposure to AI-assisted dev tools.
- **Bank of America** ([US posting](https://careers.bankofamerica.com/en-us/students/job-detail/14418/global-technology-summer-analyst-2027-software-engineer-and-mainframe-analyst-multiple-locations)): CS or related degree; 3.5 GPA preferred; front-end, back-end, or full-stack teams; mentions relational databases and CI/CD.

**Fintech-specific skills LedgerCore builds:** double-entry correctness, atomic postings, idempotency (the "exactly once" charge), concurrency on balances, reconciliation, auditability.

**Transferable skills:** REST API design, relational schema design, SQL, transactions, integration testing, CI, performance measurement, written design docs.

**On AI tools:** several postings now mention AI-assisted development. Using a tool well, meaning you design the work and can defend every line, is relevant experience. Shipping code you can't explain counts against you.

**What LedgerCore will not teach you (say this honestly in interviews):**
- DSA under time pressure, which is the main gate at all four companies. Practice it separately.
- Real distributed consensus or replication (LedgerCore runs on one database).
- Low-latency C++ (relevant for Bloomberg-type roles).
- Security: authentication, authorization, PCI.
- Cloud operations: deployment, on-call, Kubernetes.
- Real payment rails: ACH, card networks, settlement.

**Possible later projects:** a small C++ order-book or matching-engine simulation if Bloomberg-type roles are a serious target, or a deployed LedgerCore with authentication.

LedgerCore alone does not guarantee an internship. What it gives you is a deep, defensible story for the "tell me about a project" part of interviews.

---

## Part 7 — First Study Session

1. **Open:** [Spring's "Building a RESTful Web Service" guide](https://spring.io/guides/gs/rest-service). Read it without copying it into LedgerCore, and note where it differs from Boot 4.
2. **Read:** [Maven: Introduction to the Build Lifecycle](https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html), the "Build Lifecycle Basics" section only.
3. **Build:** `PingController` in `com.ledgercore.system`: `GET /api/ping` → `{"status":"ok"}`, using a `record`.
4. **Verify:**
   - `curl -i localhost:8080/api/ping` returns 200 with the JSON body.
   - A POST returns the status you **predicted beforehand**.
   - The commit is on a branch renamed to `main`.
   - You can explain in two sentences how the request reached your method.
