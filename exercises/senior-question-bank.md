# Senior Testing Question Bank

## Strategy
1. Design a risk-based test strategy for payments.
2. Compare pyramid and trophy without dogma.
3. Which checks block PRs vs run later?
4. How do you measure suite health?
5. When is removing tests an improvement?

## Boundaries
6. Define all test levels using boundaries.
7. Choose layers for checkout, upload and permissions.
8. Which framework behaviors need real framework tests?
9. Where should serialization compatibility be tested?
10. Where should DB isolation behavior be tested?

## Doubles/design
11. Diagnose an over-mocked suite.
12. Explain fake vs stub vs spy vs mock.
13. When is interaction verification correct?
14. How can fakes drift from production?
15. How do contracts control drift?

## Data/infrastructure
16. Explain rollback-per-test limitations.
17. Design parallel DB isolation.
18. When is Testcontainers worth it?
19. How should migrations be validated?
20. Test a deadlock retry policy.

## Async/concurrency
21. Replace sleep with deterministic coordination.
22. Test debounce with virtual time.
23. Test simultaneous writes for lost updates.
24. Test timeout/retry without real waiting.
25. Test cancellation.

## Quality techniques
26. Give three property invariants for a parser.
27. What can mutation reveal that coverage cannot?
28. Use coverage without gaming.
29. What are equivalent mutants?
30. When should snapshots be avoided?

## Performance/security
31. Build a realistic API workload.
32. Explain open vs closed workloads.
33. Why can p95 improve while users still report latency?
34. Design authorization invariant tests.
35. Explain scanner limitations.

## Flakiness/distributed
36. Debug a 1% flaky test.
37. Define safe quarantine.
38. Test duplicate delivery.
39. Test eventual consistency without sleeps.
40. Test idempotency across storage + messaging.

## Staff-level synthesis
41. Reduce a 60-minute suite without reducing confidence.
42. Decide whether a microservice needs E2E.
43. Turn incidents into a test-investment roadmap.
44. Design test architecture for a polyglot platform.
45. How does strategy change with deployment frequency?
46. How do observability and testing complement each other?
47. Define ownership for flaky/failing tests.
48. Review a proposed 90% coverage gate.
49. Which risks cannot be economically eliminated pre-production?
50. Present a five-minute strategy using risk, evidence and economics.
