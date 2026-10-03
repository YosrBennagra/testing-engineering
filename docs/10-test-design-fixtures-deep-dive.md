# 10 — Test Design, Fixtures and Maintainability: Deep Dive

## Wall Note / A4

- A test should explain a rule, not merely exercise a method.
- Design around **behavioral seams** and explicit ownership.
- Fixture complexity is architecture feedback.
- Prefer valid-default builders + explicit overrides.
- One test should have one reason to fail, not necessarily one assertion.
- Assertion quality matters more than assertion count.
- Test helpers must improve domain readability without hiding causality.
- Refactor tests when production code changes shape but behavior stays stable.

## Detailed Notes

### 1. Start from a risk statement

Weak starting point:

> "We need tests for OrderService."

Strong starting point:

> "A retry must not charge a customer twice, even if the first provider response times out after the provider accepted the charge."

The second statement immediately suggests:
- idempotency is the invariant;
- timeout is a required failure mode;
- the payment-provider boundary matters;
- storage semantics probably matter;
- one pure unit test will be insufficient.

A senior test design session begins with **failure classes**, not file names.

### 2. Behavioral seams

A behavioral seam is a boundary where one meaningful behavior can be stimulated and observed without unnecessarily pulling in the entire system.

Common seams:
- pure domain policy;
- application service;
- repository adapter;
- HTTP endpoint;
- message handler;
- frontend component;
- independently deployable service.

Choose the seam that exposes the risk with the least accidental infrastructure.

### 3. Arrange–Act–Assert is useful, but not a religion

AAA improves readability when the phases are conceptually distinct. It becomes noise when every test has dozens of setup lines.

Bad:

```java
var customer = new Customer();
customer.setId(UUID.randomUUID());
customer.setName("A");
customer.setStatus(ACTIVE);
// 35 more lines...
```

Better:

```java
var customer = aCustomer()
    .active()
    .withCreditLimit(eur("500"))
    .build();
```

The builder should create a **valid object by default**. Tests override only facts relevant to the scenario.

### 4. Fixture design

Prefer this hierarchy:

1. local literals for tiny values;
2. domain factory functions/builders;
3. reusable scenario fixtures for genuinely recurring domain states;
4. database fixture infrastructure only when persistence is part of the boundary.

Avoid one global fixture graph that every test mutates.

#### Valid-default builder

```java
final class OrderBuilder {
  private CustomerId customer = CustomerId.random();
  private List<OrderLine> lines = List.of(line("SKU-1", 1, eur("20")));
  private OrderStatus status = DRAFT;

  OrderBuilder paid() {
    this.status = PAID;
    return this;
  }

  OrderBuilder withLine(String sku, int qty, Money price) {
    this.lines = List.of(line(sku, qty, price));
    return this;
  }

  Order build() {
    return new Order(OrderId.random(), customer, lines, status);
  }
}
```

A builder is harmful if it silently generates facts that the test later depends on. If currency, locale or tenant affects behavior, make it visible.

### 5. Test data ownership

Ask:
- Who creates test data?
- Who deletes it?
- Can two tests use the same identifier?
- Can test order change outcomes?
- Can parallel CI collide?

Good isolation patterns:
- generated stable-per-test IDs;
- schema/database-per-test-class;
- transaction rollback when commit semantics do not matter;
- disposable infrastructure;
- explicit cleanup where real external systems are unavoidable.

### 6. State-based vs interaction-based assertions

Prefer state/output assertions when the behavior is about the resulting state.

Use interaction assertions when the interaction **is** the contract:
- exactly one event must be published;
- no notification must be sent;
- a security audit entry must be emitted;
- an external command must contain an idempotency key.

Do not assert every collaborator call "for coverage." That freezes implementation.

### 7. One reason to fail

This principle is more useful than "one assertion per test."

A test can legitimately assert:
- status changed to PAID;
- payment ID persisted;
- one receipt event emitted.

Those are all consequences of one behavior: **successful payment finalization**.

A test becomes unfocused when it also checks unrelated profile updates, metrics formatting and cache invalidation.

### 8. Failure messages are part of test architecture

Assertions should tell an engineer:
- what behavior failed;
- what expected invariant was;
- what observed values matter.

Prefer domain-specific assertion helpers where they add clarity:

```java
assertThat(order)
    .isPaid()
    .hasPaymentReference(providerRef)
    .hasNoOutstandingBalance();
```

Do not build enormous fluent DSLs that obscure normal assertions.

### 9. Test helper failure modes

Helpers become harmful when they:
- perform hidden network/database calls;
- swallow exceptions;
- choose random values without exposing seeds;
- mutate global state;
- contain production logic duplicated from the SUT;
- assert internally in surprising places.

The reader must be able to reconstruct causality.

### 10. Refactoring tests

Safe reasons to refactor:
- remove duplicated setup;
- move infrastructure lifecycle to one place;
- replace call-order mocks with behavior assertions;
- split a broad test into risk-focused cases;
- improve names;
- delete redundant tests.

Do not preserve low-value tests merely because they are old.

## Practical Example — idempotent application service

Risk: duplicate delivery must produce one logical effect.

```java
@Test
void duplicate_command_is_idempotent() {
  var payments = new InMemoryPayments();
  var gateway = new RecordingGateway();
  var service = new PaymentService(payments, gateway);

  var command = new Charge(
      "idem-42",
      CustomerId.of("C-7"),
      eur("25.00")
  );

  service.handle(command);
  service.handle(command);

  assertThat(payments.findByIdempotencyKey("idem-42")).hasSize(1);
  assertThat(gateway.chargesFor("idem-42")).hasSize(1);
}
```

What this test still cannot prove:
- a real DB uniqueness constraint exists;
- two concurrent transactions cannot both win;
- provider retry semantics are correct.

Those risks belong at integration/component levels.

## Senior Review Checklist

Before approving a test:
- Which risk does it cover?
- Could a simpler boundary detect the same risk?
- Is setup exposing or hiding relevant state?
- Is the oracle behavior-based?
- Can it run alone and in parallel?
- Is time/randomness controlled?
- Is failure diagnostic?
- Will a refactor that preserves behavior break it?
- Does an existing test already cover the same failure class?

## Exercises / Senior Questions

1. Refactor a 70-line fixture into domain-oriented builders without hiding relevant facts.
2. Find three interaction assertions that could be replaced with state/output assertions.
3. Design data isolation for 200 integration tests running eight-way parallel.
4. Explain when a shared fixture is safer than per-test setup.
5. Review a test helper that retries failures automatically: what information can it destroy?
6. Give an example where multiple assertions still represent one reason to fail.

## Related / Prerequisite Links

- [Foundations](00-foundations.md)
- [Test levels](02-test-levels.md)
- [Senior strategy](09-senior-strategy.md)
- [Programming principles](https://github.com/YosrBennagra/programming-principles)
