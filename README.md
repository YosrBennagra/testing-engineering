# Testing Engineering — 0 → Expert

A practical knowledge base for designing trustworthy software-testing systems. The goal is not "write more tests"; it is to build **fast, deterministic, risk-driven evidence** that software behaves correctly across boundaries.

> Master index: [software-engineer-roadmap](https://github.com/YosrBennagra/software-engineer-roadmap)

## Learning order

1. [Foundations](docs/00-foundations.md)
2. [Strategy, pyramid/trophy and risk](docs/01-strategy-and-test-shapes.md)
3. [Unit → integration → component → API → E2E](docs/02-test-levels.md)
4. [Test doubles, TDD and BDD](docs/03-doubles-tdd-bdd.md)
5. [Contracts, databases and Testcontainers](docs/04-contract-db-testcontainers.md)
6. [Frontend, async and concurrency testing](docs/05-frontend-async-concurrency.md)
7. [Property-based, mutation and coverage](docs/06-property-mutation-coverage.md)
8. [Performance and security testing](docs/07-performance-security.md)
9. [Flakiness, CI gates and distributed systems](docs/08-flakiness-ci-distributed.md)
10. [Senior test architecture and strategy](docs/09-senior-strategy.md)

Use the [A4 wall note](wall-notes/testing-a4.md) for recall and the [senior question bank](exercises/senior-question-bank.md) for review.

## Topic map

| Area | Owned here | Cross-link instead of duplicate |
|---|---|---|
| Test design | strategy, levels, doubles, determinism | [programming-principles](https://github.com/YosrBennagra/programming-principles) |
| API testing | API-level evidence, contracts | [api-engineering](https://github.com/YosrBennagra/api-engineering) |
| Database testing | isolation, transactions, realistic infra | [database-engineering](https://github.com/YosrBennagra/database-engineering) |
| Frontend testing | component/user behavior boundaries | [angular-mastery](https://github.com/YosrBennagra/angular-mastery) |
| Security testing | test strategy and placement | [application-security](https://github.com/YosrBennagra/application-security) |
| Performance | workload/SLO evidence | [system-design](https://github.com/YosrBennagra/system-design) |
| CI gates | testing signal and gate design | [devops-platform-engineering](https://github.com/YosrBennagra/devops-platform-engineering) |

## Progress checklist

- [ ] Explain testing as an evidence system, not a coverage contest.
- [ ] Choose test boundaries from risk and economics.
- [ ] Distinguish unit, integration, component, API and E2E by boundary.
- [ ] Use mocks/stubs/spies/fakes deliberately.
- [ ] Apply TDD/BDD without ceremony.
- [ ] Design contract tests for independently deployable services.
- [ ] Test database/transaction behavior with realistic infrastructure.
- [ ] Use Testcontainers where dependency semantics matter.
- [ ] Test frontend behavior without implementation coupling.
- [ ] Test async/concurrent behavior without arbitrary sleeps.
- [ ] Apply property-based and mutation testing.
- [ ] Design load/stress/soak/spike tests.
- [ ] Place security tests appropriately.
- [ ] Diagnose and eliminate flaky tests.
- [ ] Use coverage and CI gates as signals, not vanity metrics.
- [ ] Test retries, partial failure, duplication and eventual consistency.
- [ ] Build maintainable senior-level test architecture.

## Repository principles

- Prefer **observable behavior** over implementation detail.
- Prefer the **lowest-cost test that can disprove the relevant risk**.
- Use real dependencies when semantics matter; use controlled doubles when speed/control matters.
- A passing test is useful only if it can fail for the right reasons.
- Flakiness is a defect in the test system.
- Domain theory belongs in its owning repository; this repo links to it.

## Expert bar

An expert can explain not only *how* to write a test, but why it belongs at a boundary, what false confidence it may create, how it behaves under parallel CI, what defect class it detects, and whether its maintenance cost is justified.


## Expert deep dives

After the core sequence, use these as the production-level depth pass:

11. [Test design, fixtures and maintainability](docs/10-test-design-fixtures-deep-dive.md)
12. [Contracts, databases and Testcontainers in practice](docs/11-contract-db-testcontainers-practice.md)
13. [Async, concurrency and distributed-system testing](docs/12-async-concurrency-distributed-practice.md)
14. [CI gates, performance, security and suite economics](docs/13-ci-performance-security-strategy.md)
15. [Property-based, mutation and fuzz testing in practice](docs/14-property-mutation-fuzzing-deep-dive.md)
16. [Frontend, component and E2E testing architecture](docs/15-frontend-component-e2e-deep-dive.md)

These chapters intentionally revisit earlier topics at a deeper level: boundary selection, lifecycle, failure injection, parallelism, diagnostic quality, CI economics, generative test techniques, browser/component boundaries and the specific failure modes senior engineers are expected to reason about.
