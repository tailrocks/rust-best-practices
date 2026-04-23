---
name: rust-best-practices
description: Apply idiomatic Rust engineering guidance when Codex writes, reviews, refactors, or explains Rust code. Use this skill for .rs files, Cargo workspaces, new Rust projects, public API design, ownership and borrowing decisions, error handling, Clippy/rustfmt setup, testing strategy, documentation, performance-sensitive code, unsafe or thread-safety review, and readability or architecture feedback.
---

# Rust Best Practices

Use this skill to write Rust code that is readable, explicit, testable,
performant by design, and aligned with the Rust ecosystem. Prefer the project's
existing conventions first; when the project is new or has no stronger rule,
apply the guidance in this skill.

## Workflow

1. Inspect context before changing code: `Cargo.toml`, crate type, workspace
   layout, public API boundaries, feature flags, existing lint config,
   `rustfmt.toml`, tests, docs, and dependency policy.
2. Load the reference that matches the decision:
   - `references/review-checklist.md` for PR review, audits, or final checks.
   - `references/tooling-lints.md` for `cargo fmt`, Clippy, rustdoc lints,
     lint configuration, CI commands, and profiling tools.
   - `references/ownership-performance.md` for borrowing, cloning, allocation,
     iterators, dispatch, stack/heap choices, pointers, and shared state.
   - `references/api-design.md` for public APIs, naming, traits, constructors,
     feature flags, type safety, type-state, and future compatibility.
   - `references/errors-testing-docs.md` for `Result`, panics, error crates,
     async error bounds, tests, doc tests, snapshots, comments, and rustdoc.
   - `references/readability-style-architecture.md` for file layout, imports,
     naming, control flow, helper functions, dependency boundaries, and
     maintainable project structure.
3. Make the smallest coherent change. Separate API boundary changes, dependency
   additions, and broad refactors from local implementation edits when practical.
4. Validate with project-appropriate commands. Prefer:
   - `cargo fmt --check`
   - `cargo clippy --all-targets --all-features --locked -- -D warnings`
   - `cargo test --all-targets --all-features --locked`
   Adjust flags when features are mutually exclusive, the project has no
   lockfile, slow tests require opt-in, or the repository documents a custom
   `just`, `make`, or `xtask` command.
5. Report residual risk: commands not run, warnings suppressed, missing
   negative tests, public API compatibility questions, or performance claims not
   backed by measurement.

## Core Rules

### Ownership and Allocation

- Prefer borrowed parameters when ownership is not required: `&str` over
  `String`, `&[T]` over `Vec<T>`, `&Path` over `PathBuf`, and `Option<&T>` over
  `&Option<T>`.
- Take ownership when the function stores, moves, sends, or would otherwise
  clone the value. Let the caller decide where cloning happens.
- Treat `.clone()` as a design decision. Do not clone just to appease the borrow
  checker.
- Avoid intermediate `Vec` or `String` allocations when iterators, slices,
  borrowed views, or lazy fallback closures are enough.
- Prefer clear control flow over clever iterator or combinator chains when the
  chain hides errors, branching, ownership, or side effects.

### Errors and Panics

- Return `Result<T, E>` for expected failure. Use `Option<T>` only when absence
  has no meaningful error detail.
- Reserve `panic!`, `unwrap`, and `expect` for tests, examples with hidden setup,
  unreachable invariants, or programmer errors with precise context.
- Prefer typed errors for libraries. Use `anyhow`-style errors mainly at binary,
  CLI, application, prototype, or test-helper boundaries.
- Use `?` for propagation when it preserves useful context. Add context at IO,
  parsing, network, task, or user-facing boundaries.
- Document public failure behavior with `# Errors`, `# Panics`, and `# Safety`
  sections where relevant.

### API Design

- Follow Rust naming conventions and ownership semantics: `snake_case` values,
  `UpperCamelCase` types, `SCREAMING_SNAKE_CASE` constants, and meaningful
  `as_`/`to_`/`into_` conversions.
- Implement common traits when they make semantic sense: `Debug`, `Clone`,
  `Copy`, `Eq`, `Hash`, `Default`, `Display`, `Error`, `From`, `TryFrom`,
  `AsRef`, and `AsMut`.
- Encode invariants in types: newtypes, enums, builders, config structs,
  type-state, and validated wrappers beat ambiguous `bool`, `Option`, `String`,
  or primitive parameters.
- Keep public fields private unless exposing representation is part of the stable
  contract. Use sealed traits when downstream implementations would block future
  evolution.
- Avoid public dependencies, re-exports, blanket generics, and serialization
  derives unless they are intentional compatibility commitments.

### Tests and Documentation

- Tests should be readable examples of behavior. Use precise names, minimal
  fixtures, and success plus failure-path coverage.
- Prefer behavior tests at stable boundaries instead of tests that freeze
  incidental helper APIs.
- Use doc tests for public API examples. Example code should propagate errors
  with `?` instead of `unwrap` unless the unwrap is the point of the example.
- Use snapshots for generated, rendered, serialized, or CLI output that is easier
  to review as a whole value. Keep snapshots deterministic and redacted.
- Comments explain why: invariants, safety, compatibility, platform behavior,
  performance tradeoffs, or external constraints. Prefer clearer code and tests
  over comments that restate the implementation.

### Lints and Suppression

- Run Clippy as a design review assistant, not as a formatting substitute.
- Fix warnings before suppressing them. If suppression is justified, prefer
  `#[expect(clippy::lint_name)]` with a short reason so stale suppressions are
  caught later.
- Do not enable broad lint groups such as all `restriction`, `pedantic`, or
  `nursery` wholesale without project agreement; apply strict lints selectively.
- Avoid crate-level `#![deny(warnings)]` in reusable libraries. Prefer CI flags
  or explicit lint configuration to avoid surprise breakage from future compiler
  warnings.

### Readability and Architecture

- Optimize for first-time readers: public or entry-point items first, important
  types before helpers, imports grouped consistently, and implementation details
  near their use.
- Keep control flow local and visible. Express preconditions in types or adjacent
  checks rather than splitting validation and use across distant helpers.
- Prefer boring, searchable names over abbreviations. Use short names only for
  established conventions such as `db`, `ctx`, `acc`, `idx`, `res`, or `it`.
- Add dependencies conservatively. A small helper crate still carries compile
  time, maintenance, supply-chain, and public API cost.
- Separate stable boundaries from unstable internals. IO, serialization,
  protocols, public types, feature flags, and re-exports deserve explicit
  boundary design.

## Review Output

When reviewing Rust code, lead with actionable findings ordered by severity.
For each finding, include the file/line, impact, violated Rust principle or local
convention, and a practical fix. Then list validation commands and remaining
test or API risks.

When writing or refactoring Rust code, briefly state the local convention
followed and the validation performed. Do not claim performance wins unless
measured or the change removes an obvious allocation or clone in ordinary code.
