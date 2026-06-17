---
name: clean-tests
description: Use when writing, fixing, editing, or refactoring Java tests. Enforces Clean Code principles—fast tests, boundary coverage, one assert per test.
when_to_use: |
  Also trigger on: slow or flaky tests, `@Disabled` without a clear reason, tests that only cover the happy path, tests with multiple assertions about different concepts, missing boundary cases (empty input, off-by-one, page zero), or asks about "coverage gap", "edge case", "did we test".
---

# Clean Tests

## T1: Insufficient Tests

Test everything that could possibly break. Use coverage tools as a guide, not a goal.

```java
// Bad - only tests happy path
@Test
void divide() {
    assertEquals(5, divide(10, 2));
}

// Good - tests edge cases too
@Test
void divideNormal() {
    assertEquals(5, divide(10, 2));
}

@Test
void divideByZero() {
    assertThrows(ArithmeticException.class, () -> divide(10, 0));
}

@Test
void divideNegative() {
    assertEquals(-5, divide(-10, 2));
}
```

## T2: Use a Coverage Tool

Coverage tools report gaps in your testing strategy. Don't ignore them.

```bash
# Run with coverage (JaCoCo plugin bound to the test phase)
mvn test

# Aim for meaningful coverage, not 100%
```

## T3: Don't Skip Trivial Tests

Trivial tests document behavior and catch regressions. They're worth more than their cost.

```java
// Worth having - documents expected behavior
@Test
void userDefaultRole() {
    User user = new User("Alice");
    assertEquals("member", user.role());
}
```

## T4: An Ignored Test Is a Question About an Ambiguity

Don't use `@Disabled` to hide problems. Either fix the test or delete it.

```java
// Bad - hiding a problem
@Disabled("flaky, fix later")
@Test
void asyncOperation() {
    ...
}

// Good - either fix it or document why it's skipped
@Disabled("Requires Redis, see CONTRIBUTING.md for setup")
@Test
void cacheInvalidation() {
    ...
}
```

## T5: Test Boundary Conditions

Bugs congregate at boundaries. Test them explicitly.

```java
@Test
void paginationBoundaries() {
    List<Integer> items = IntStream.range(0, 100).boxed().toList();

    // First page
    assertEquals(items.subList(0, 10), paginate(items, 1, 10));

    // Last page
    assertEquals(items.subList(90, 100), paginate(items, 10, 10));

    // Beyond last page
    assertEquals(List.of(), paginate(items, 11, 10));

    // Page zero (invalid)
    assertThrows(IllegalArgumentException.class, () -> paginate(items, 0, 10));

    // Empty list
    assertEquals(List.of(), paginate(List.of(), 1, 10));
}
```

## T6: Exhaustively Test Near Bugs

When you find a bug, write tests for all similar cases. Bugs cluster.

```java
// Found bug: off-by-one in date calculation
// Now test ALL date boundaries
@Test
void monthBoundaries() {
    assertEquals(31, lastDayOfMonth(2024, 1));  // January
    assertEquals(29, lastDayOfMonth(2024, 2));  // Leap year February
    assertEquals(28, lastDayOfMonth(2023, 2));  // Non-leap February
    assertEquals(30, lastDayOfMonth(2024, 4));  // 30-day month
    assertEquals(31, lastDayOfMonth(2024, 12)); // December
}
```

## T7: Patterns of Failure Are Revealing

When tests fail, look for patterns. They often point to deeper issues.

```java
// If all async tests fail intermittently,
// the problem isn't the tests—it's the async handling
```

## T8: Test Coverage Patterns Can Be Revealing

Look at which code paths are untested. Often they reveal design problems.

```java
// If you can't easily test a method, it probably does too much
// Refactor for testability
```

## T9: Tests Should Be Fast

Slow tests don't get run. Keep unit tests under 100ms each.

```java
// Bad - hits real database
@Test
void userCreation() {
    Database db = connectToDatabase();  // Slow!
    User user = db.createUser("Alice");
    assertEquals("Alice", user.name());
}

// Good - uses mock or in-memory
@Test
void userCreation() {
    Database db = new InMemoryDatabase();
    User user = db.createUser("Alice");
    assertEquals("Alice", user.name());
}
```

## Test Organization

### F.I.R.S.T. Principles

- **Fast**: Tests should run quickly
- **Independent**: Tests shouldn't depend on each other
- **Repeatable**: Same result every time, any environment
- **Self-Validating**: Pass or fail, no manual inspection
- **Timely**: Written before or with the code, not after

### One Concept Per Test

```java
// Bad - testing multiple things
@Test
void user() {
    User user = new User("Alice", "alice@example.com");
    assertEquals("Alice", user.name());
    assertEquals("alice@example.com", user.email());
    assertTrue(user.isValid());
    user.activate();
    assertTrue(user.isActive());
}

// Good - one concept each
@Test
void userStoresName() {
    User user = new User("Alice", "alice@example.com");
    assertEquals("Alice", user.name());
}

@Test
void userStoresEmail() {
    User user = new User("Alice", "alice@example.com");
    assertEquals("alice@example.com", user.email());
}

@Test
void newUserIsValid() {
    User user = new User("Alice", "alice@example.com");
    assertTrue(user.isValid());
}

@Test
void userCanBeActivated() {
    User user = new User("Alice", "alice@example.com");
    user.activate();
    assertTrue(user.isActive());
}
```

## Quick Reference

| Rule | Principle                         |
| ---- | --------------------------------- |
| T1   | Test everything that could break  |
| T2   | Use coverage tools                |
| T3   | Don't skip trivial tests          |
| T4   | Ignored test = ambiguity question |
| T5   | Test boundary conditions          |
| T6   | Exhaustively test near bugs       |
| T7   | Look for patterns in failures     |
| T8   | Check coverage when debugging     |
| T9   | Tests must be fast (<100ms)       |
