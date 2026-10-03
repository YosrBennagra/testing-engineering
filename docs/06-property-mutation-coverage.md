# 06 — Property-Based Testing, Mutation Testing and Coverage

## Wall Note / A4

- Example tests prove examples; property tests challenge invariants.
- Shrinking finds a small counterexample.
- Mutation asks whether tests detect plausible code damage.
- Coverage shows executed structure, not correctness.
- Coverage is diagnostic, not a target to game.

## Detailed Notes

### Property-based testing

Define invariants over generated input:
- encode/decode round-trip;
- sort result is ordered + permutation;
- serialize/deserialize preserves supported state;
- applying inverse operation restores state;
- domain invariant never breaks.

Generators must cover realistic edge cases. Record seeds/counterexamples.

### Mutation testing

Mutation tools flip conditions, alter constants or remove calls. A surviving mutant means the suite did not detect that change.

Caveats: equivalent mutants, runtime cost and noise in generated/trivial code. Apply selectively to important logic.

### Coverage

Line/branch coverage answers "what executed?" It does not prove meaningful assertions.

Useful use:
- find untouched risk areas;
- detect coverage regressions;
- inspect branch/condition coverage for decision-heavy code;
- transparently exclude generated/uninteresting code.

## Practical Example

```java
@Property
void roundTripPreservesMoney(
    @ForAll @BigRange(min="0", max="100000000") BigInteger cents) {
  var value = Money.ofCents(cents);
  assertThat(codec.read(codec.write(value))).isEqualTo(value);
}
```

## Exercises / Senior Questions

1. Write three properties for pagination.
2. Explain shrinking.
3. What does 90% line coverage tell you—and not tell you?
4. Which modules merit mutation testing?
5. How do you prevent coverage gate gaming?

## Related / Prerequisite Links

- [Programming principles](https://github.com/YosrBennagra/programming-principles)
