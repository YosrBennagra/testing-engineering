# Testing Engineering — A4 Wall Note

## Core model

**Risk → Boundary → Stimulus → Oracle → Isolation → Feedback**

- Test behavior, not implementation.
- Choose the cheapest boundary that can expose the risk.
- Real dependency when semantics matter; fake when control/speed matters.
- Determinism > sleeps/retries.
- A passing test matters only if it can fail for the right reason.

## Levels

- **Unit:** rules, algorithms, invariants.
- **Integration:** DB/framework/protocol semantics.
- **Component/API:** real service boundary, controlled externals.
- **Contract:** independently changing consumer/provider agreement.
- **E2E:** few critical deployed journeys.

## Doubles

Stub = canned answer.  
Spy = records calls.  
Mock = interaction expectation.  
Fake = lightweight working substitute.

## Advanced

Property = invariant over generated inputs.  
Mutation = would tests catch plausible code damage?  
Coverage = execution map, **not correctness**.  
Load = expected traffic. Stress = beyond capacity. Soak = duration. Spike = rapid change.

## Flake checklist

Time? randomness? shared state? ordering? race? external service? resource starvation? eventual consistency?

Never normalize "rerun until green."

## Distributed systems

Test timeout, retry, duplicate, reorder, partial failure, stale read, failover and idempotency.

## Before adding a test

1. What risk?
2. Why this boundary?
3. What must be real?
4. What is the oracle?
5. Can it run alone + parallel?
6. What is its cost?
7. Is this evidence already covered?
