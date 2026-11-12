# LedgerCore — Free Resource Library

This list has one primary resource per topic, plus at most one supplement. Every resource was free when it was added.

**Verification key** (status as of 2026-11-11):
- ✅ Confirmed by web search to exist at this URL, and to be free where that was in question.
- 📘 Long-standing official documentation. Not searched individually, but the vendor's own free docs.
- ⚠️ Partially verified. See the note.

Search returned mostly snippets, not full pages. A ✅ means the resource exists and covers the stated topic. It does not mean every chapter was read end to end.

Your local PostgreSQL is **16**, so the links below point at `/docs/16/` pages.

---

## Java, Maven, Spring

| # | Resource (Org) | Format · Time · Level | Study exactly this | Week | Verified |
|---|---|---|---|---|---|
| 1 | [Building a RESTful Web Service](https://spring.io/guides/gs/rest-service) (Spring) | Guide · 30 min · Beginner | Whole guide. Read it, don't copy it into LedgerCore. | W1 | 📘 |
| 2 | [Building a REST API with Spring Boot](https://spring.academy/courses/building-a-rest-api-with-spring-boot) (Spring Academy) | Course · ~5 hr · Beginner | Lessons on the REST controller, Spring Data, and testing | W1–W4 | ⚠️ The course listing was only confirmed through a secondary copy. Spring Academy Pro content became free in April 2024 ([InfoQ](https://www.infoq.com/news/2024/04/spring-academy-pro-free/)) for anyone who registers with a work or **educational** email. |
| 3 | [Introduction to the Build Lifecycle](https://maven.apache.org/guides/introduction/introduction-to-the-lifecycle.html) (Apache Maven) | Docs · 30 min · Beginner | Lifecycles, phases, goals | W1 | 📘 |
| 4 | [Accessing Data with JPA](https://spring.io/guides/gs/accessing-data-jpa) (Spring) | Guide · 45 min · Beginner | Do it in a scratch project, not in LedgerCore | W2–W4 | ✅ |
| 5 | [Spring Framework: Transaction Management](https://docs.spring.io/spring-framework/reference/data-access/transaction.html) + [Rolling Back](https://docs.spring.io/spring/reference/data-access/transaction/declarative/rolling-back.html) | Docs · 2 hr · Intermediate | Declarative transactions, `@Transactional`, rollback rules, propagation | W5 | ✅ |
| 6 | [Spring Boot Reference: Testing](https://docs.spring.io/spring-boot/reference/testing/index.html) + [Test Modules (4.0)](https://docs.spring.io/spring-boot/4.0/reference/testing/test-modules.html) | Docs · 1.5 hr · Intermediate | Test slices (`@WebMvcTest`, `@DataJpaTest`), Testcontainers support | W1, W4 | ✅ test modules / 📘 index |
| 7 | [Spring MVC Error Responses (ProblemDetail)](https://docs.spring.io/spring-framework/reference/web/webmvc/mvc-ann-rest-exceptions.html) + [Bean Validation](https://docs.spring.io/spring-framework/reference/core/validation/beanvalidation.html) | Docs · 1 hr · Intermediate | RFC 9457 problem details, `@Valid` | W4 | 📘 |
| 8 | [Java Tutorials: Concurrency](https://docs.oracle.com/javase/tutorial/essential/concurrency/) (Oracle) + [Virtual Threads](https://dev.java/learn/new-features/virtual-threads) (dev.java) | Tutorial · 3 hr · Intermediate | Synchronization, Liveness (deadlock), High-Level Concurrency Objects (executors, atomics, `CountDownLatch`) | W6 | ✅ The Oracle tutorial was written for JDK 8, but the fundamentals still hold. |

## PostgreSQL and Databases

| # | Resource | Format · Time · Level | Study exactly this | Week | Verified |
|---|---|---|---|---|---|
| 9 | [PostgreSQL 16 Tutorial](https://www.postgresql.org/docs/16/tutorial.html) | Docs · 3 hr · Beginner | Ch. 2 (The SQL Language), Ch. 3 §3.3 Foreign Keys, §3.4 **Transactions** | W2 | 📘 |
| 10 | [PGExercises](https://pgexercises.com/) (Alisdair Owens) | Interactive · 3 hr · Beginner→Intermediate | Basic, Joins, Aggregates (skip Recursive) | W2 | ✅ Free, runs in the browser |
| 11 | [PG 16: Constraints](https://www.postgresql.org/docs/16/ddl-constraints.html) | Docs · 1 hr · Beginner | CHECK, NOT NULL, UNIQUE, FK | W3 | 📘 |
| 12 | [PG 16: Transaction Isolation](https://www.postgresql.org/docs/16/transaction-iso.html) + [Explicit Locking](https://www.postgresql.org/docs/16/explicit-locking.html) | Docs · 2.5 hr · Intermediate | §13.2 (all levels), §13.3.2 row-level locks, §13.3.4 deadlocks | W6–W7 | ✅ Chapter structure confirmed |
| 13 | [CMU 15-445 Intro to Database Systems](https://15445.courses.cs.cmu.edu/fall2024/) (Andy Pavlo) | Lecture videos · 4 × 80 min · Advanced | **Only:** Concurrency Control Theory, Two-Phase Locking, MVCC (optionally Logging/Recovery). Pick lectures by title, since numbering changes each semester. | W5–W7 | ⚠️ The lectures are confirmed free on YouTube. The Fall 2024 course URL was not verified. |
| 14 | [Hermitage](https://github.com/ept/hermitage) (Martin Kleppmann) | Repo · 1.5 hr · Intermediate | `postgres.md`: reproduce the anomalies in two `psql` windows | W6 | ✅ |
| 15 | [Lost update & locking guide](https://vladmihalcea.com/a-beginners-guide-to-database-locking-and-the-lost-update-phenomena) + [Optimistic vs. Pessimistic](https://vladmihalcea.com/optimistic-vs-pessimistic-locking) (Vlad Mihalcea) | Articles · 1 hr · Intermediate | Both articles. The blog is free; skip his paid courses. | W7 | ✅ |
| 16 | [Use The Index, Luke](https://use-the-index-luke.com/sql) (Markus Winand) | Free online book · 3 hr · Intermediate | "Anatomy of an Index", "The Where Clause", the Execution Plans appendix (PostgreSQL) | W10 | ✅ The web edition is free; the print book is paid. |

## Ledger Engineering (fintech)

| # | Resource | Format · Time · Level | Study exactly this | Week | Verified |
|---|---|---|---|---|---|
| 17 | Accounting for Developers — [Part I](https://moderntreasury.com/journal/accounting-for-developers-part-i), [Part II](https://moderntreasury.com/journal/accounting-for-developers-part-ii) (Modern Treasury) | Articles · 1.5 hr · Beginner | Part I (debits/credits, normal balances), Part II (Venmo-style ledger) | W3 | ✅ |
| 18 | How to Scale a Ledger — [I](https://moderntreasury.com/journal/how-to-scale-a-ledger-part-i), [II](https://moderntreasury.com/journal/how-to-scale-a-ledger-part-ii), [IV](https://moderntreasury.com/journal/how-to-scale-a-ledger-part-iv), [V](https://moderntreasury.com/journal/how-to-scale-a-ledger-part-v), [VI](https://moderntreasury.com/journal/how-to-scale-a-ledger-part-vi) (Modern Treasury) | Article series · 3 hr · Intermediate | I, II, V in W3; IV (recording/authorization) in W5; VI (concurrency controls) in W7 | W3, W5, W7 | ✅ Free online. The ebook may require an email. |
| 19 | [Books: an immutable double-entry accounting database service](https://developer.squareup.com/blog/books-an-immutable-double-entry-accounting-database-service/) (Square) | Article · 30 min · Intermediate | Whole article. Compare its design with yours. | W3 | ✅ |
| 20 | [Ledger: Stripe's system for tracking and validating money movement](https://stripe.dev/blog/ledger-stripe-system-for-tracking-and-validating-money-movement) (Stripe) | Article · 45 min · Intermediate→Advanced | Whole article (reconciliation at scale) | W9 | ✅ |
| 21 | [Designing robust and predictable APIs with idempotency](https://stripe.com/blog/idempotency) (Stripe) + [Idempotent Requests docs](https://docs.stripe.com/api/idempotent_requests) | Article + docs · 45 min · Intermediate | Whole article, then the API contract | W8 | ✅ article / 📘 docs |
| 22 | [Implementing Stripe-like Idempotency Keys in Postgres](https://brandur.org/idempotency-keys) (Brandur Leach) | Long article · 2 hr · Advanced | Whole article. **The single most important resource for W8.** | W8 | ✅ |
| 23 | [Immutability Changes Everything](https://ceres.cs.umd.edu/818/papers/immutability.pdf) (Pat Helland, CIDR 2015) | Paper · 1 hr · Intermediate | The sections on accountants and append-only ledgers | W9 | ✅ The mirror exists; the PDF itself was not opened. |

## Reliability and Distributed Systems (only after transactions)

| # | Resource | Format · Time · Level | Study exactly this | Week | Verified |
|---|---|---|---|---|---|
| 24 | [Making retries safe with idempotent APIs](https://aws.amazon.com/builders-library/making-retries-safe-with-idempotent-APIs/) (Amazon Builders' Library) | Article · 45 min · Intermediate | Whole article | W8 | ✅ |
| 25 | [Timeouts, retries, and backoff with jitter](https://aws.amazon.com/builders-library/timeouts-retries-and-backoff-with-jitter) (Amazon Builders' Library) | Article · 45 min · Intermediate | Whole article | W9 | ✅ |
| 26 | [Distributed Systems lectures + notes](https://www.cl.cam.ac.uk/teaching/2122/ConcDisSys/materials.html) (Martin Kleppmann, Cambridge) | Video + notes · ~2 hr · Intermediate | **Only:** Lecture 1 (Introduction), Lecture 2 (Models: two generals, system models, fault tolerance), the two-phase commit part of Lecture 7. Confirm the numbering against the notes' table of contents. | W9 | ✅ The videos are public on YouTube. |

## Testing, Performance, Tooling

| # | Resource | Format · Time · Level | Study exactly this | Week | Verified |
|---|---|---|---|---|---|
| 27 | [JUnit User Guide](https://docs.junit.org/current/user-guide/) | Docs · 1.5 hr · Beginner | Writing Tests (annotations, assertions, `@ParameterizedTest`). Boot 4 ships JUnit Jupiter 6. | W1+ | 📘 |
| 28 | [Getting started with Testcontainers in a Java Spring Boot Project](https://testcontainers.com/guides/testing-spring-boot-rest-api-using-testcontainers/) | Guide · 1.5 hr · Intermediate | Whole guide. Prefer `@ServiceConnection` (Boot 3.1+) over `@DynamicPropertySource`. | W4 | ✅ |
| 29 | [Flyway documentation](https://documentation.red-gate.com/flyway) + Spring Boot database-initialization docs | Docs · 1 hr · Beginner | Versioned migrations, naming (`V1__...sql`) | W2 | ⚠️ Official docs URL not confirmed by search. Undo migrations are a paid feature, and you won't need them. |
| 30 | [Grafana k6: Get started](https://grafana.com/docs/k6/latest/get-started/) | Docs · 2 hr · Beginner | Write your first test, Running k6, thresholds | W10 | ✅ k6 is open source; the cloud version is optional. |
| 31 | [Spring Boot Actuator](https://docs.spring.io/spring-boot/reference/actuator/index.html) + [PG 16: Using EXPLAIN](https://www.postgresql.org/docs/16/using-explain.html) | Docs · 2 hr · Intermediate | Endpoints, Micrometer metrics, reading EXPLAIN ANALYZE | W10 | 📘 |
| 32 | [Missing Semester: Version Control](https://missing.csail.mit.edu/2020/version-control/) (MIT) + [Pro Git](https://git-scm.com/book) ch. 2–3 | Lecture + book · 2.5 hr · Beginner | Git's data model; branching | W1, W11 | ✅ / 📘 |
| 33 | [Tech Interview Handbook: Grind 75](https://www.techinterviewhandbook.org/grind75) (Yangshun Tay) | Problem planner · ongoing | Generate a plan for ~3 hr/week | DSA (separate from this project) | ✅ |

---

## Deliberately excluded
- *Designing Data-Intensive Applications* and *Java Concurrency in Practice*: excellent, but paid books.
- Baeldung paid courses. Its free articles are fine as a quick reference.
- Udemy and any subscription-only content.

## Maintaining this file
- When a link breaks, replace it and update the Verified column with the date.
- Add a resource only if it **replaces** a weaker one or covers a topic nothing else here covers.
