# 09 — Senior Test Architecture and Maintainability

## Wall Note / A4

- Test code is production engineering infrastructure.
- Fixtures/builders should express domain intent.
- Test architecture must support parallelism, determinism and diagnosis.
- Track runtime, flake rate, defect yield and maintenance burden.
- Delete redundant tests when confidence is preserved.

## Detailed Notes

### Architecture

Mature suites typically separate:
- domain builders/fixtures;
- infrastructure lifecycle;
- protocol clients/drivers;
- test data factories;
- assertion helpers;
- scenario DSLs only where they genuinely reduce noise.

Avoid a global "test utilities" dumping ground with hidden mutable state.

### Fixtures

Prefer minimal relevant state, valid-default builders, explicit overrides and deliberate setup channels.

Use public APIs for setup when the API behavior matters. Use direct DB setup when speed is more important and persistence shape is not the subject.

### Maintainability

Good name:
`transfer_is_rejected_when_daily_limit_would_be_exceeded`

Weak name:
`testTransfer2`

Legitimate refactoring includes deleting duplicate assertions, stale behavior, implementation-coupled mocks and snapshots nobody reviews.

### Organizational strategy

Align testing investment with:
- ownership;
- incident history;
- architecture boundaries;
- release frequency;
- business/regulatory risk;
- observability.

Post-incident question: **which cheaper pre-production test or production invariant could detect this failure class?**

### Decision framework

Before adding a test:

1. What risk?
2. What must be real?
3. Cheapest reliable oracle?
4. Deterministic + parallel?
5. Expected lifetime/cost?
6. Actionable failure?
7. Duplicate evidence?

## Practical Example

A 45-minute flaky checkout E2E suite should not gain retries. Decompose:
- pricing → unit;
- constraints → DB integration;
- auth/validation → API;
- payment request → contract/integration;
- browser wiring → component;
- one deployed happy path → E2E.

## Exercises / Senior Questions

1. What suite-health metrics matter?
2. When should a test be deleted?
3. Design test architecture for 30 services.
4. Reduce 60-minute CI to 15 without reducing confidence.
5. Explain strategy using risk/economics rather than tool names.

## Related / Prerequisite Links

- [Software architecture](https://github.com/YosrBennagra/software-architecture)
- [System design](https://github.com/YosrBennagra/system-design)
