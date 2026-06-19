---
name: csharp-clean-code
description: "Clean Code catalog for C# (rules C/E/F/G/N/T/CS/CT) plus the Boy Scout Rule. Use when writing, reviewing, refactoring, or auditing C# (*.cs) for naming, functions, comments, DRY, boundaries, error handling, tests, nullable annotations, XML docs, and primary constructors."
applyTo: "**/*.cs"
---

<!-- markdownlint-disable-file -->

# C# Clean Code — Boy Scout Rule

> "Always leave the campground cleaner than you found it." — Robert Baden-Powell
> "Always check a module in cleaner than when you checked it out." — Robert C. Martin, _Clean Code_

You don't have to make every file perfect — just **a little bit better** than you found it.

> **Scope of this rule.** The proportionality guidance below applies when you are _editing or
> implementing_ a change in this code. It does **not** govern a dedicated review, audit, or
> assessment pass: when a task is to _review_ or _catalog_ code quality (for example the Clean
> Code Reviewer agent), identify **every** opportunity exhaustively and do not limit yourself to
> one small improvement.

## While Editing C# Code

1. Complete the requested change first.
2. Then make at least one small, proportional improvement:
   - **Quick wins:** rename an unclear variable (N1), delete a redundant comment (C3),
     remove dead code / unused usings (G9, CS1), replace a magic number with a named
     constant (G25), extract a deeply nested block into a well-named method (G30).
   - **Deeper (when time allows):** split a method that does multiple things (F1-F4, G30),
     remove duplication (G5), add missing boundary checks (G3, G33), improve test
     coverage (T1-T9), add missing XML documentation (CS3).
3. Report each cleanup by rule ID, e.g. "Also cleaned up: renamed `x` → `results` (N1)".
4. Keep changes proportional to the task — do not make large unrelated rewrites.

**Don't:** leave code worse than you found it; say "that's not my code"; wait for a
refactor sprint; make massive changes unrelated to your task.

**Do:** make one small improvement with every change; fix what you see; keep changes
proportional; leave a trail of rule-ID'd improvements.

### The Rule in Practice

```csharp
// Asked to fix a bug in this method — don't just fix it and leave:
List<double> Proc(List<double> d, List<double> x, bool flag)
{
    foreach (var i in d)
    {
        if (i > 0)
        {
            if (flag) { x.Add(i * 1.0825); } else { x.Add(i); }
        }
    }
    return x;
}

// Leave it cleaner:
private const double TaxRate = 0.0825;

/// <summary>Filters positive values and optionally applies tax.</summary>
/// <param name="values">The source values to filter.</param>
/// <param name="applyTax">When <see langword="true"/>, adds <see cref="TaxRate"/> to each value.</param>
/// <returns>A new list containing the filtered, optionally taxed values.</returns>
public static List<double> ProcessPositiveValues(IEnumerable<double> values, bool applyTax = false)
{
    var rate = applyTax ? 1 + TaxRate : 1.0;
    return values.Where(v => v > 0).Select(v => v * rate).ToList();
}
```

✅ Descriptive method name (N1) · clear parameter names (N1) · PascalCase method (G24) ·
named constant (G25) · no output-argument mutation (F2) · XML documentation (CS3)

---

## Clean Code Catalog

Enforces all Clean Code principles from Robert C. Martin's Chapter 17, adapted for C#.
When reviewing code, identify violations by rule number (e.g., "G5 violation: duplicated logic").

### Comments (C1-C5)

- C1: No metadata in comments (use Git)
- C2: Delete obsolete comments immediately
- C3: No redundant comments
- C4: Write comments well if you must
- C5: Never commit commented-out code

### Environment (E1-E2)

- E1: One command to build (`dotnet build`)
- E2: One command to test (`dotnet test`)

### Functions (F1-F4)

- F1: Maximum 3 arguments (use records or parameter objects for more)
- F2: No output arguments (return values; avoid `ref`/`out` at boundaries)
- F3: No flag arguments (split methods)
- F4: Delete dead methods

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
- G20: Method names say what they do
- G21: Understand the algorithm
- G22: Make dependencies physical
- G23: Prefer polymorphism to if/else chains
- G24: Follow conventions (HVE C# Standards + `dotnet format` / StyleCop / Roslyn analyzers)
- G25: Named constants, not magic numbers
- G26: Be precise
- G27: Structure over convention
- G28: Encapsulate conditionals
- G29: Avoid negative conditionals
- G30: Methods do one thing
- G31: Make temporal coupling explicit
- G32: Don't be arbitrary
- G33: Encapsulate boundary conditions
- G34: One abstraction level per method
- G35: Config at high levels
- G36: Law of Demeter (no train wrecks)

### C#-Specific (CS1-CS4)

These rules encode the HVE C# coding standards and modern C# idioms:

- CS1: File-scoped namespaces (`namespace Company.Project.Feature;`) aligned to folder structure; no block-scoped namespace unless a file contains multiple unrelated types
- CS2: Nullable reference types enabled project-wide (`<Nullable>enable</Nullable>`); annotate with `?`, `[NotNull]`/`[MaybeNull]`/`[NotNullWhen(bool)]` for complex flows; use `required` modifier for non-nullable properties without defaults; avoid the null-forgiving operator (`!`) except when APIs lack nullable annotations or preceding validation guarantees non-null
- CS3: XML documentation (`///`) required on all `public` and `protected` members; use `<inheritdoc/>` on interface implementations and overrides; document parameters (`<param>`), return values (`<returns>`), and thrown exceptions (`<exception cref="...">`) on public methods
- CS4: Prefer records for data-only types, primary constructors for straightforward initialization, and pattern matching (`is`, `switch` expressions) over type-checks plus casts; use collection expressions (`[..existing, item]`) and `Span<T>`/`ReadOnlySpan<T>` for array operations

### Names (N1-N7)

Follow HVE C# naming conventions:

| Element            | Convention       | Example                |
| ------------------ | ---------------- | ---------------------- |
| Classes/Files      | `PascalCase`     | `UserService.cs`       |
| Interfaces         | `IPascalCase`    | `IRepository`          |
| Methods/Properties | `PascalCase`     | `ProcessAsync`         |
| Private fields     | `_camelCase`     | `_logger`, `_isActive` |
| Constants          | `PascalCase`     | `TaxRate`, `MaxRetry`  |
| Base classes       | `PascalCaseBase` | `WidgetBase`           |
| Type parameters    | `TName`          | `TEntity`              |

- N1: Choose descriptive names
- N2: Right abstraction level
- N3: Use standard nomenclature
- N4: Unambiguous names
- N5: Name length matches scope
- N6: No encodings (no Hungarian notation like `strName`, `bFlag`)
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

### C# Test Conventions (CT1-CT5)

These rules apply specifically to test code (`*.Tests` projects):

- CT1: BDD-style test names: `GivenContext_WhenAction_ExpectedResult` (e.g., `GivenEmptyInput_ProcessAsync_ThrowsArgumentException`)
- CT2: XUnit as test framework; NSubstitute (preferred) or FakeItEasy for mocking; Moq only in existing projects pinned to `4.18.x` or `4.20.2+`
- CT3: Arrange/Act/Assert structure — blank line between each phase; label with comments only when the boundary is non-obvious
- CT4: Name the system under test `_sut`; order other `readonly` fields alphabetically by name after the underscore prefix (`_httpClient` before `_sut`)
- CT5: One assertion per test; use `Assert.Equivalent` for structural object equality; never verify logger mocks

### Quick Reference Table

| Category      | Rule | One-Liner                             |
| ------------- | ---- | ------------------------------------- |
| **Comments**  | C1   | No metadata (use Git)                 |
|               | C3   | No redundant comments                 |
|               | C5   | No commented-out code                 |
| **Functions** | F1   | Max 3 arguments                       |
|               | F3   | No flag arguments                     |
|               | F4   | Delete dead methods                   |
| **General**   | G5   | DRY—no duplication                    |
|               | G9   | Delete dead code                      |
|               | G16  | No obscured intent                    |
|               | G23  | Polymorphism over if/else chains      |
|               | G25  | Named constants, not magic numbers    |
|               | G30  | Methods do one thing                  |
|               | G36  | Law of Demeter (one dot)              |
| **C#**        | CS1  | File-scoped namespaces                |
|               | CS2  | Nullable enabled; `required` modifier |
|               | CS3  | XML docs on public/protected members  |
|               | CS4  | Records, primary ctors, pattern match |
| **Names**     | N1   | Descriptive names                     |
|               | N5   | Name length matches scope             |
| **Tests**     | T5   | Test boundary conditions              |
|               | T9   | Tests must be fast                    |
| **CT**        | CT1  | BDD test names                        |
|               | CT4  | `_sut` for system under test          |
|               | CT5  | One assertion per test                |

### Anti-Patterns (Don't → Do)

| ❌ Don't                                   | ✅ Do                                         |
| ------------------------------------------ | --------------------------------------------- |
| Comment every line                         | Delete obvious comments                       |
| Block-scoped namespace with extra indent   | File-scoped namespace `namespace Foo.Bar;`    |
| `string? name = null!`                     | `required string Name { get; set; }`          |
| Magic number `86400`                       | `private const int SecondsPerDay = 86400;`    |
| `Process(data, true)`                      | `ProcessVerbose(data)`                        |
| Deep nesting                               | Guard clauses, early returns                  |
| `obj.A.B.C.Value`                          | `obj.GetValue()`                              |
| 100+ line method                           | Split by responsibility                       |
| `public string Name;` (public field)       | `public string Name { get; set; }` (property) |
| `(Type)obj` without type-check             | `obj is Type t ? t.Method() : default`        |
| `var x = new List<string>() { "a", "b" };` | `List<string> x = ["a", "b"];`                |

### AI Behavior

When reviewing code, identify violations by rule number (e.g., "G5 violation: duplicated logic").
When fixing or editing code, report what was fixed (e.g., "Fixed: extracted magic number to `SecondsPerDay` (G25)").

---

## Examples by Rule

For worked before/after examples for every rule family, see [examples](references/examples.md).
