---
name: java-clean-code
description: "Clean Code catalog for Java (rules C/E/F/G/N/T/J) plus the Boy Scout Rule. Use when writing, reviewing, refactoring, or auditing Java (*.java) for naming, functions, comments, DRY, boundaries, error handling, and tests."
applyTo: "**/*.java"
---

<!-- markdownlint-disable-file -->

# Java Clean Code — Boy Scout Rule

> "Always leave the campground cleaner than you found it." — Robert Baden-Powell
> "Always check a module in cleaner than when you checked it out." — Robert C. Martin, _Clean Code_

You don't have to make every file perfect — just **a little bit better** than you found it.

> **Scope of this rule.** The proportionality guidance below applies when you are _editing or
> implementing_ a change in this code. It does **not** govern a dedicated review, audit, or
> assessment pass: when a task is to _review_ or _catalog_ code quality (for example the Clean
> Code Reviewer agent), identify **every** opportunity exhaustively and do not limit yourself to
> one small improvement.

## While Editing Java Code

1. Complete the requested change first.
2. Then make at least one small, proportional improvement:
   - **Quick wins:** rename an unclear variable (N1), delete a redundant comment (C3),
     remove dead code / unused imports (G9, J1), replace a magic number with a named
     constant (G25), extract a deeply nested block into a well-named method (G30).
   - **Deeper (when time allows):** split a method that does multiple things (F1-F4, G30),
     remove duplication (G5), add missing boundary checks (G3, G33), improve test
     coverage (T1-T9).
3. Report each cleanup by rule ID, e.g. "Also cleaned up: renamed `x` → `results` (N1)".
4. Keep changes proportional to the task — do not make large unrelated rewrites.

**Don't:** leave code worse than you found it; say "that's not my code"; wait for a
refactor sprint; make massive changes unrelated to your task.

**Do:** make one small improvement with every change; fix what you see; keep changes
proportional; leave a trail of rule-ID'd improvements.

### The Rule in Practice

```java
// Asked to fix a bug in this method — don't just fix it and leave:
List<Double> proc(List<Double> d, List<Double> x, boolean flag) {
    for (double i : d) {
        if (i > 0) {
            if (flag) { x.add(i * 1.0825); } else { x.add(i); }
        }
    }
    return x;
}

// Leave it cleaner:
static final double TAX_RATE = 0.0825;

/** Filter positive values, optionally applying tax. */
List<Double> processPositiveValues(List<Double> values, boolean applyTax) {
    double rate = applyTax ? 1 + TAX_RATE : 1;
    return values.stream().filter(v -> v > 0).map(v -> v * rate).toList();
}
```

✅ Descriptive method name (N1) · clear parameter names (N1) · explicit types ·
named constant (G25) · no output-argument mutation (F2) · useful Javadoc (C4)

## Clean Code Catalog

Enforces all Clean Code principles from Robert C. Martin's Chapter 17, adapted for Java.
When reviewing code, identify violations by rule number (e.g., "G5 violation: duplicated logic").

### Comments (C1-C5)

- C1: No metadata in comments (use Git)
- C2: Delete obsolete comments immediately
- C3: No redundant comments
- C4: Write comments well if you must
- C5: Never commit commented-out code

### Environment (E1-E2)

- E1: One command to build (`mvn compile`)
- E2: One command to test (`mvn test`)

### Functions (F1-F4)

- F1: Maximum 3 arguments (use records for more)
- F2: No output arguments (return values)
- F3: No flag arguments (split functions)
- F4: Delete dead functions

### General (G1-G36)

- G1: One language per file
- G2: Implement expected behavior
- G3: Handle boundary conditions
- G4: Don't override safeties
- G5: DRY - no duplication
- G6: Consistent abstraction levels
- G7: Base classes don't know children
- G8: Minimize public interface
- G9: Delete dead code
- G10: Variables near usage
- G11: Be consistent
- G12: Remove clutter
- G13: No artificial coupling
- G14: No feature envy
- G15: No selector arguments
- G16: No obscured intent
- G17: Code where expected
- G18: Prefer instance methods
- G19: Use explanatory variables
- G20: Function names say what they do
- G21: Understand the algorithm
- G22: Make dependencies physical
- G23: Prefer polymorphism to if/else
- G24: Follow conventions (Google Java Style + Checkstyle/Spotless)
- G25: Named constants, not magic numbers
- G26: Be precise
- G27: Structure over convention
- G28: Encapsulate conditionals
- G29: Avoid negative conditionals
- G30: Functions do one thing
- G31: Make temporal coupling explicit
- G32: Don't be arbitrary
- G33: Encapsulate boundary conditions
- G34: One abstraction level per function
- G35: Config at high levels
- G36: Law of Demeter (no train wrecks)

### Java-Specific (J1-J3)

Robert Martin's original Java-specific rules, modernized for current Java conventions:

- J1: Keep imports explicit; no wildcard imports — supersedes Clean Code's original "use wildcards" advice
- J2: Don't inherit constants; use `static final` on a dedicated type or static imports
- J3: Use enums (and records/sealed types), not magic constants

### Names (N1-N7)

- N1: Choose descriptive names
- N2: Right abstraction level
- N3: Use standard nomenclature
- N4: Unambiguous names
- N5: Name length matches scope
- N6: No encodings
- N7: Names describe side effects

### Tests (T1-T9)

- T1: Test everything that could break
- T2: Use coverage tools
- T3: Don't skip trivial tests
- T4: Ignored test = ambiguity question
- T5: Test boundary conditions
- T6: Exhaustively test near bugs
- T7: Look for patterns in failures
- T8: Check coverage when debugging
- T9: Tests must be fast (< 100ms each)

### Quick Reference Table

| Category      | Rule | One-Liner                          |
| ------------- | ---- | ---------------------------------- |
| **Comments**  | C1   | No metadata (use Git)              |
|               | C3   | No redundant comments              |
|               | C5   | No commented-out code              |
| **Functions** | F1   | Max 3 arguments                    |
|               | F3   | No flag arguments                  |
|               | F4   | Delete dead functions              |
| **General**   | G5   | DRY—no duplication                 |
|               | G9   | Delete dead code                   |
|               | G16  | No obscured intent                 |
|               | G23  | Polymorphism over if/else          |
|               | G25  | Named constants, not magic numbers |
|               | G30  | Functions do one thing             |
|               | G36  | Law of Demeter (one dot)           |
| **Names**     | N1   | Descriptive names                  |
|               | N5   | Name length matches scope          |
| **Tests**     | T5   | Test boundary conditions           |
|               | T9   | Tests must be fast                 |

### Anti-Patterns (Don't → Do)

| ❌ Don't                              | ✅ Do                                       |
| ------------------------------------- | ------------------------------------------- |
| Comment every line                    | Delete obvious comments                     |
| Helper for one-liner                  | Inline the code                             |
| Wildcard import `import java.util.*;` | Explicit import                             |
| Magic number `86400`                  | `static final long SECONDS_PER_DAY = 86400` |
| `process(data, true)`                 | `processVerbose(data)`                      |
| Deep nesting                          | Guard clauses, early returns                |
| `obj.a.b.c.value`                     | `obj.getValue()`                            |
| 100+ line function                    | Split by responsibility                     |

### AI Behavior

When reviewing code, identify violations by rule number (e.g., "G5 violation: duplicated logic").
When fixing or editing code, report what was fixed (e.g., "Fixed: extracted magic number to `SECONDS_PER_DAY` (G25)").

## Examples by Rule

For worked before/after examples for every rule family, see [examples](references/examples.md).
