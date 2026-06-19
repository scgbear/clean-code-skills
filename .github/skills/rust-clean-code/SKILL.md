---
name: rust-clean-code
description: "Clean Code catalog for Rust (rules C/E/F/G/N/T/RS/RT) plus the Boy Scout Rule. Use when writing, reviewing, refactoring, or auditing Rust (*.rs) for naming, functions, comments, DRY, boundaries, error handling, tests, ownership, doc comments, and async patterns."
applyTo: "**/*.rs"
---

<!-- markdownlint-disable-file -->

# Rust Clean Code — Boy Scout Rule

> "Always leave the campground cleaner than you found it." — Robert Baden-Powell
> "Always check a module in cleaner than when you checked it out." — Robert C. Martin, _Clean Code_

You don't have to make every file perfect — just **a little bit better** than you found it.

> **Scope of this rule.** The proportionality guidance below applies when you are _editing or
> implementing_ a change in this code. It does **not** govern a dedicated review, audit, or
> assessment pass: when a task is to _review_ or _catalog_ code quality (for example the Clean
> Code Reviewer agent), identify **every** opportunity exhaustively and do not limit yourself to
> one small improvement.

## While Editing Rust Code

1. Complete the requested change first.
2. Then make at least one small, proportional improvement:
   - **Quick wins:** rename an unclear variable (N1), delete a redundant comment (C3),
     remove dead code / unused imports (G9, RS1), replace a magic number with a named
     constant (G25), extract a deeply nested block into a well-named function (G30).
   - **Deeper (when time allows):** split a function that does multiple things (F1-F4, G30),
     remove duplication (G5), add missing boundary checks (G3, G33), improve test
     coverage (T1-T9), add missing doc comments (RS3).
3. Report each cleanup by rule ID, e.g. "Also cleaned up: renamed `x` → `results` (N1)".
4. Keep changes proportional to the task — do not make large unrelated rewrites.

**Don't:** leave code worse than you found it; say "that's not my code"; wait for a
refactor sprint; make massive changes unrelated to your task.

**Do:** make one small improvement with every change; fix what you see; keep changes
proportional; leave a trail of rule-ID'd improvements.

### The Rule in Practice

```rust
// Asked to fix a bug in this function — don't just fix it and leave:
fn proc(d: &[f64], x: &mut Vec<f64>, flag: bool) {
    for i in d {
        if *i > 0.0 {
            if flag { x.push(i * 1.0825); } else { x.push(*i); }
        }
    }
}

// Leave it cleaner:
const TAX_RATE: f64 = 0.0825;

/// Filters positive values and optionally applies tax.
///
/// # Returns
///
/// A new vector containing the filtered, optionally taxed values.
pub fn process_positive_values(values: &[f64], apply_tax: bool) -> Vec<f64> {
    let rate = if apply_tax { 1.0 + TAX_RATE } else { 1.0 };
    values.iter().filter(|&&v| v > 0.0).map(|&v| v * rate).collect()
}
```

✅ Descriptive function name (N1) · clear parameter names (N1) · `snake_case` (G24) ·
named constant (G25) · no output-argument mutation (F2) · doc comment with `# Returns` (RS3)

---

## Clean Code Catalog

Enforces all Clean Code principles from Robert C. Martin's Chapter 17, adapted for Rust.
When reviewing code, identify violations by rule number (e.g., "G5 violation: duplicated logic").

### Comments (C1-C5)

- C1: No metadata in comments (use Git)
- C2: Delete obsolete comments immediately
- C3: No redundant comments
- C4: Write comments well if you must
- C5: Never commit commented-out code

### Environment (E1-E2)

- E1: One command to build (`cargo build`)
- E2: One command to test (`cargo test`)

### Functions (F1-F4)

- F1: Maximum 3 arguments (use structs or builder types for more)
- F2: No output arguments — return values; avoid `&mut` parameters at public boundaries; prefer owned return types or `impl Into<T>`
- F3: No flag arguments (split functions)
- F4: Delete dead functions

### General (G1-G36)

- G1: One language per file
- G2: Implement expected behavior
- G3: Handle boundary conditions
- G4: Don't override safeties
- G5: DRY - no duplication
- G6: Consistent abstraction levels
- G7: Base types don't know subtypes
- G8: Minimize public interface (default to private; expose only the crate's external contract)
- G9: Delete dead code
- G10: Variables near usage
- G11: Be consistent
- G12: Remove clutter
- G13: No artificial coupling
- G14: No feature envy
- G15: No selector arguments
- G16: No obscured intent
- G17: Code where expected
- G18: Prefer methods over free functions when state is involved
- G19: Use explanatory variables
- G20: Function names say what they do
- G21: Understand the algorithm
- G22: Make dependencies physical
- G23: Prefer polymorphism to if/else chains (use traits + dispatch instead of `match` on type tags)
- G24: Follow conventions (HVE Rust Standards + `cargo fmt` + `cargo clippy`)
- G25: Named constants, not magic numbers (`const` or `static`)
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
- G36: Law of Demeter — no chained field access across module boundaries

### Rust-Specific (RS1-RS5)

These rules encode the HVE Rust coding standards and idiomatic Rust patterns:

- RS1: Module structure — keep `lib.rs`/`main.rs` thin (declarations and re-exports only); organize by domain when needed (`config`, `error`, `handlers`, `models`, `services`); unit tests co-located in `#[cfg(test)] mod tests`; integration tests in `tests/`; module member ordering: `use` → constants → types → traits → impls → free functions → test module
- RS2: Error handling — use `thiserror` for all module and library errors; define a module-scoped `pub type Result<T> = std::result::Result<T, YourError>;`; propagate with `?`; never `unwrap()`/`expect()` in production paths — reserve `expect()` with a descriptive message for startup/initialization only; use `anyhow` only in application-level `main` or CLI entry points
- RS3: Doc comments (`///`) required on all `pub` items; `//!` for module-level docs at the top of `lib.rs` or `mod.rs`; include `# Examples` for public API functions, `# Errors` for `Result`-returning functions, and `# Panics` for functions that can panic
- RS4: Naming — `PascalCase` for types, traits, and enum variants; `snake_case` for functions, variables, and modules; `SCREAMING_SNAKE_CASE` for constants and statics; type suffixes: `*Error`, `*Config`, `*Builder`; crate names in `kebab-case`
- RS5: Async — use Tokio; prefer `?` for propagation; never block the async runtime with synchronous I/O — use `tokio::task::spawn_blocking` for CPU-bound work; use `tokio::select!` for racing tasks and `tokio::try_join!` for collecting concurrent results; use `async-trait` for 2021-edition trait definitions requiring async methods

### Names (N1-N7)

Follow HVE Rust naming conventions:

| Element       | Convention             | Example                       |
| ------------- | ---------------------- | ----------------------------- |
| Types/Structs | `PascalCase`           | `UserService`, `DeviceConfig` |
| Traits        | `PascalCase`           | `Repository`, `Serializer`    |
| Enum variants | `PascalCase`           | `Status::Active`              |
| Functions     | `snake_case`           | `process_request`             |
| Variables     | `snake_case`           | `device_url`, `retry_count`   |
| Constants     | `SCREAMING_SNAKE_CASE` | `DEFAULT_TIMEOUT`             |
| Modules       | `snake_case`           | `error_handler`               |
| Crate names   | `kebab-case`           | `my-service`                  |

- N1: Choose descriptive names
- N2: Right abstraction level
- N3: Use standard nomenclature
- N4: Unambiguous names
- N5: Name length matches scope
- N6: No encodings (no Hungarian notation or type-prefix mangling)
- N7: Names describe side effects

### Tests (T1-T9)

- T1: Test everything that could break
- T2: Use coverage tools (`cargo llvm-cov` or `cargo tarpaulin`)
- T3: Don't skip trivial tests
- T4: Ignored test = ambiguity question
- T5: Test boundary conditions
- T6: Exhaustively test near bugs
- T7: Look for patterns in failures
- T8: Check coverage when debugging
- T9: Tests must be fast (< 100ms each for unit tests)

### Rust Test Conventions (RT1-RT5)

These rules apply specifically to test code:

- RT1: BDD-style test names in `snake_case`: `given_context_when_action_then_expected` or a descriptive behavior statement (e.g., `given_valid_input_parse_returns_config`, `when_endpoint_unavailable_send_returns_error`)
- RT2: `mockall` (preferred) for trait-based mocking via `#[automock]`; `wiremock` for async HTTP server mocking; `mockito` for lightweight HTTP mocking; all test dependencies in `[dev-dependencies]`
- RT3: Co-locate unit tests in `#[cfg(test)] mod tests` at the bottom of the source file under test; place integration tests in the `tests/` directory; use fixture helper functions for test data instead of repeating inline construction
- RT4: Use `#[tokio::test]` for async tests; use `#[test]` for synchronous tests
- RT5: One assertion per test; include assertion messages explaining intent: `assert!(result.is_ok(), "should parse valid JSON")`

### Quick Reference Table

| Category      | Rule | One-Liner                                                |
| ------------- | ---- | -------------------------------------------------------- |
| **Comments**  | C1   | No metadata (use Git)                                    |
|               | C3   | No redundant comments                                    |
|               | C5   | No commented-out code                                    |
| **Functions** | F1   | Max 3 arguments                                          |
|               | F3   | No flag arguments                                        |
|               | F4   | Delete dead functions                                    |
| **General**   | G5   | DRY—no duplication                                       |
|               | G9   | Delete dead code                                         |
|               | G16  | No obscured intent                                       |
|               | G23  | Traits + dispatch over `match` on type tags              |
|               | G25  | Named constants, not magic numbers                       |
|               | G30  | Functions do one thing                                   |
|               | G36  | Law of Demeter (no chained field access)                 |
| **Rust**      | RS1  | Module structure; thin entry points                      |
|               | RS2  | `thiserror` errors; `?` propagation; no `unwrap` in prod |
|               | RS3  | `///` on all `pub` items; `# Errors`/`# Panics`          |
|               | RS4  | Naming: `PascalCase` types, `snake_case` fns             |
|               | RS5  | Tokio async; `spawn_blocking` for sync I/O               |
| **Names**     | N1   | Descriptive names                                        |
|               | N5   | Name length matches scope                                |
| **Tests**     | T5   | Test boundary conditions                                 |
|               | T9   | Tests must be fast                                       |
| **RT**        | RT1  | BDD test names in `snake_case`                           |
|               | RT2  | `mockall` / `wiremock` in `[dev-dependencies]`           |
|               | RT5  | One assertion per test                                   |

### Anti-Patterns (Don't → Do)

| ❌ Don't                                 | ✅ Do                                                         |
| ---------------------------------------- | ------------------------------------------------------------- |
| Comment every line                       | Delete obvious comments                                       |
| `unwrap()` in production code            | `?` operator with `thiserror` error types                     |
| Magic number `86400`                     | `const SECONDS_PER_DAY: u64 = 86_400;`                        |
| `fn process(data: &Data, verbose: bool)` | `fn process(data: &Data)` + `fn process_verbose(data: &Data)` |
| Deep nesting                             | Guard clauses with early `return Err(...)` / `let...else`     |
| `obj.field.inner.value`                  | method on the outer type                                      |
| 100+ line function                       | Split by responsibility                                       |
| `println!` / `eprintln!` in production   | `tracing::info!` / `tracing::error!`                          |
| `String` parameter when `&str` suffices  | `fn foo(s: &str)` or `fn foo(s: impl Into<String>)`           |
| `expect("err")` in request-handling code | `?` propagation                                               |

### AI Behavior

When reviewing code, identify violations by rule number (e.g., "G5 violation: duplicated logic").
When fixing or editing code, report what was fixed (e.g., "Fixed: extracted magic number to `SECONDS_PER_DAY` (G25)").

---

## Examples by Rule

For worked before/after examples for every rule family, see [examples](references/examples.md).
