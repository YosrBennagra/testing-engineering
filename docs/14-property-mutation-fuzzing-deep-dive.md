# 14 — Property-Based, Mutation and Fuzz Testing: Deep Dive

## Wall Note / A4

- Example tests sample cases; property tests sample **input space**.
- A property expresses an invariant, algebraic law or relation—not the implementation.
- Generators determine what your property test can discover.
- Shrinking turns a large failure into a minimal counterexample.
- Mutation testing measures whether tests detect plausible code changes.
- Fuzzing is strongest at parsers, protocols and hostile/untrusted-input boundaries.
- Reproduce every generated failure with its seed/input.
- Combine techniques: examples for intent, properties for breadth, mutation for test strength, fuzzing for robustness.

## Detailed Notes

### 1. What makes a good property

A good property survives many implementation strategies.

Examples:

**Round trip**

```text
decode(encode(x)) == x
```

**Idempotence**

```text
normalize(normalize(x)) == normalize(x)
```

**Commutativity**, where domain-valid:

```text
merge(a, b) == merge(b, a)
```

**Metamorphic relation**

If every price is multiplied by 2, the computed subtotal should multiply by 2, assuming no nonlinear rule applies.

**Invariant**

After any valid sequence of reservations:

```text
availableStock >= 0
```

Do not write:

> For every x, result equals the same algorithm copied into the test.

That only duplicates the implementation.

### 2. Generator quality is test quality

A property cannot find values the generator never creates.

For pagination, a useful generator includes:
- zero rows;
- one row;
- page exactly full;
- one over page boundary;
- very large page index;
- duplicate sort keys;
- Unicode filters;
- invalid/negative size if the API accepts raw input;
- maximum permitted page size.

Use weighted generation when rare edge cases matter.

### 3. Valid vs invalid domains

Separate generators when semantics differ:

```text
ValidEmail
InvalidEmailSyntax
ValidButUnknownAccountEmail
OversizedInput
```

This keeps properties meaningful instead of filling them with conditional guards.

### 4. Shrinking

A framework may find failure on:

```text
["x9", "a", "", "β", "a", ... 200 values]
```

Shrinking should reduce it toward the smallest failing counterexample, perhaps:

```text
["", "a"]
```

A minimal counterexample makes diagnosis much faster.

Preserve:
- seed;
- original failing input;
- shrunk failing input.

### 5. Stateful/model-based testing

For state machines, generate **sequences of commands**, not just values.

Example bank-account model:

```text
OpenAccount
Deposit(amount)
Withdraw(amount)
Freeze
Unfreeze
Close
```

The test runs the same command sequence against:
- a simple reference model;
- the real implementation;

then compares externally observable state/invariants.

This is powerful for:
- workflows;
- caches;
- storage engines;
- protocol clients;
- permission transitions.

### 6. Mutation testing

Typical mutants:
- `>` → `>=`;
- `&&` → `||`;
- remove method call;
- return constant;
- alter arithmetic;
- negate condition.

If a mutant survives, ask:

1. Is it equivalent behavior?
2. Is the mutated code irrelevant/dead?
3. Is the test missing?
4. Is the assertion too weak?

Mutation score is not an end goal. Critical business logic deserves more attention than generated mappers.

### 7. Mutation-testing workflow

Efficient use:

1. run ordinary tests;
2. mutate one important module/package;
3. inspect surviving mutants;
4. strengthen tests only where survivor represents meaningful behavior;
5. exclude generated/trivial code intentionally;
6. track regressions selectively.

Do not mutate an entire huge monorepo on every commit unless economics justify it.

### 8. Fuzz testing

Fuzzing feeds large volumes of automatically generated or mutated input into a target, often optimizing toward new execution paths or crashes.

Best targets:
- parsers;
- file formats;
- network protocols;
- deserializers;
- compilers/interpreters;
- regex-heavy validators;
- security boundaries.

Assertions may be:
- must not crash;
- must not hang;
- must stay under memory/time limits;
- invalid input must be rejected;
- parser and serializer preserve an invariant.

### 9. Property testing vs fuzzing

Property testing usually starts with **domain-aware generators + explicit properties**.

Fuzzing often starts from bytes/structured corpora and seeks crashes, coverage or unusual paths.

They overlap. The useful distinction is intent, not branding.

### 10. Differential testing

Run the same input through two implementations:

```text
reference(input) == optimized(input)
```

Useful during:
- algorithm optimization;
- migration;
- parser rewrite;
- replacing library/provider.

The reference implementation can be slow but simple.

### 11. Coverage interaction

Generated tests can execute huge input volumes while still checking a weak property.

Coverage can reveal untouched paths, but mutation testing better challenges whether the assertions are capable of detecting damage.

No single metric proves test quality.

## Practical Example — pagination properties

```java
@Property
void concatenating_all_pages_matches_sorted_dataset(
    @ForAll("datasets") List<Item> input,
    @ForAll @IntRange(min = 1, max = 100) int pageSize) {

  var expected = input.stream()
      .sorted(comparing(Item::stableKey))
      .toList();

  var actual = readAllPages(input, pageSize);

  assertThat(actual).containsExactlyElementsOf(expected);
}

@Property
void no_item_appears_twice_across_pages(
    @ForAll("datasets") List<Item> input,
    @ForAll @IntRange(min = 1, max = 100) int pageSize) {

  var actual = readAllPages(input, pageSize);

  assertThat(actual)
      .extracting(Item::id)
      .doesNotHaveDuplicates();
}
```

These properties can expose unstable ordering around equal sort keys.

## Practical Example — mutation survivor

Production:

```java
boolean canWithdraw(Money balance, Money amount) {
  return amount.compareTo(balance) <= 0;
}
```

Mutant:

```java
return amount.compareTo(balance) < 0;
```

If tests still pass, the exact-balance boundary is untested. Add the missing behavior example/property; do not merely increase line coverage.

## Failure Modes

- Generator creates mostly ordinary values.
- Property contains so many `assume` filters that few cases execute.
- Seed is not recorded.
- Property restates production algorithm.
- Teams chase mutation score by testing trivial getters.
- Equivalent mutants consume review time.
- Fuzzer crashes cannot be replayed.
- Fuzz target performs real external side effects.
- Generative tests are flaky because uncontrolled time/network is inside the boundary.

## Exercises / Senior Questions

1. Define five properties for a money type.
2. Design generators for cursor pagination with duplicate sort keys.
3. Build a model-based test for a shopping cart.
4. A mutation score is 92%. What can still be wrong with the suite?
5. Choose a fuzz target in an API service and define its oracle.
6. Explain when differential testing is safer than rewriting expected values by hand.
7. Review a property test that rejects 95% of generated inputs.

## Related / Prerequisite Links

- [Property/mutation/coverage core](06-property-mutation-coverage.md)
- [Testing foundations](00-foundations.md)
- [Application security](https://github.com/YosrBennagra/application-security)
- [Programming principles](https://github.com/YosrBennagra/programming-principles)
