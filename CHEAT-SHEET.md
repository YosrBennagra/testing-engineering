# Testing Engineering — Cheat Sheet

> A test is an executable claim about **observable behaviour**. One-page wall note: [wall-notes/testing-a4.md](wall-notes/testing-a4.md). Hub: [software-engineer-roadmap](https://github.com/YosrBennagra/software-engineer-roadmap)

```
Risk → Boundary → Stimulus → Oracle → Isolation → Feedback
```

## Test levels (level = boundary, not tool)
| Level | Proves | Java/Spring/Angular tooling |
|---|---|---|
| Unit | rules, algorithms, invariants | JUnit 5, AssertJ, Mockito · Jest/Vitest |
| Integration | real DB/framework/protocol semantics | `@DataJpaTest`, `@WebMvcTest`, **Testcontainers** |
| Component / API | one deployable with controlled externals | `@SpringBootTest` + WireMock, MockMvc/RestAssured · Angular TestBed |
| Contract | consumer ↔ provider agreement | Pact, Spring Cloud Contract, OpenAPI diff |
| E2E | a few critical deployed user journeys | Playwright, Cypress |
- **Pyramid** (many unit, fewer integration, few E2E) and **trophy** (integration-heavy) are heuristics, not quotas.
- Optimise for confidence per (runtime + maintenance cost). Don't repeat the same assertion at every level.

## Test doubles
| Double | What it does |
|---|---|
| Dummy | passed in but never used |
| Stub | returns canned answers |
| Spy | records calls (and may call through) |
| Mock | pre-programmed **interaction expectations** (verify) |
| Fake | lightweight working implementation (in-memory repo) |
- Mock at **boundaries you own** (ports). Don't mock value objects, the class under test, or every collaborator.
- Over-mocking = tests coupled to implementation → refactoring breaks tests without breaking behaviour.

## Good test anatomy
- **Arrange / Act / Assert** (Given / When / Then). One reason to fail, not necessarily one assert.
- Builders with valid defaults + explicit overrides (`anOrder().withStatus(PAID).build()`).
- Name tests after the rule: `rejectsOrderWhenStockIsInsufficient`.
- **F.I.R.S.T.** = Fast, Isolated, Repeatable, Self-validating, Timely.
- A test is only valuable if it can **fail for the right reason** (see it red first).

## TDD / BDD
- TDD: **red → green → refactor**, in tiny steps. It is a design-feedback tool, not just testing.
- BDD: shared behaviour language with the business (Given/When/Then), not syntax decoration.

## Data & infrastructure
- Use the **production DB engine** (Testcontainers), not H2, when SQL/dialect/constraints matter.
- Rollback-per-test (`@Transactional` test) hides commit-time behaviour, after-commit listeners and constraint timing.
- Each test owns its data. Avoid shared mutable fixtures, fixed ports and order dependence.
- Spring context caching: avoid needless `@MockBean`/`@MockitoBean` variants and `@DirtiesContext`.

## Advanced techniques
| Technique | Answers |
|---|---|
| Property-based (jqwik, fast-check) | does the invariant hold for generated inputs? (shrinking → minimal counterexample) |
| Mutation (PIT) | would my tests catch plausible bugs? (mutation score > coverage) |
| Coverage | which code ran. **Not correctness.** Diagnostic, not a target |
| Fuzzing | does a parser/protocol survive hostile input? |
| Approval / snapshot | large outputs. Review diffs carefully |

## Performance testing
| Type | Question |
|---|---|
| Load | OK at expected traffic? |
| Stress | where/how does it break beyond capacity? |
| Soak | stable over hours (leaks)? |
| Spike | survives sudden bursts? |
- Needs a workload model, a production-like env, warm-up, a baseline, and pass criteria (p95/p99 latency, errors, throughput, saturation). Tools: Gatling, k6, JMeter.
- Read **percentiles**, never just averages.

## Async, concurrency & distributed
- No `Thread.sleep`. Use Awaitility with bounded polling, a fake clock (`Clock` injection), RxJS `fakeAsync`/virtual time.
- Force interleavings (latches/barriers). One green run doesn't prove race-freedom.
- Inject failures at boundaries: timeout, retry, duplicate, reorder, partial failure, stale read.
- Retry tests prove **classification + backoff + idempotency**, not only "eventually succeeded".

## Frontend
- Query by role/label/text (what users perceive), not CSS structure.
- Mock the network (`HttpTestingController`, MSW), not every service.
- Accessibility checks (axe) throughout. E2E with isolated users/data per test.

## Flaky tests
**Causes:** time · randomness · shared state · test order · races · external services · resource starvation · eventual consistency.
- Never normalise "rerun until green". Quarantine only with an owner, visibility and a deadline.

## CI quality gates
- Cheap, high-signal first: compile → lint/format → unit → static analysis (Sonar) → integration → contract → E2E smoke.
- Gate on risk-based evidence, not arbitrary global coverage %.
- Track suite health: duration, queue time, flake rate, rerun rate, defect yield.

## Before adding a test
1. What risk? 2. Why this boundary? 3. What must be real? 4. What is the oracle? 5. Does it run alone + in parallel? 6. What does it cost? 7. Is this already covered?

## Senior gotchas
- 100% coverage with no meaningful assertions.
- Mocking the repository and claiming the query is tested.
- `@SpringBootTest` for every tiny test → slow suite.
- E2E tests used as the main regression net.
- Testing private methods → test through the public behaviour.
- Delete redundant tests when confidence stays the same. Test code is production infrastructure.
