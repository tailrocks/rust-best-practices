# Tooling, Lints, and Measurement

Use this reference when setting up or reviewing Rust formatting, linting,
documentation checks, CI commands, benchmarks, profiling, and lint suppressions.

## Contents

- Toolchain setup
- Formatting
- Clippy
- Lint configuration
- Rustdoc checks
- CI integration
- Benchmarking and profiling
- Suppression policy

## Toolchain Setup

- Prefer the repository's pinned toolchain first: `rust-toolchain.toml`,
  `rust-toolchain`, CI config, or project docs.
- Install standard components when missing:

```bash
rustup component add rustfmt clippy
```

- Check tool availability with:

```bash
cargo fmt --version
cargo clippy -V
```

- Avoid silently changing the toolchain channel. Nightly-only formatting or lint
  options should be an explicit project choice.

## Formatting

- Let `rustfmt` handle mechanical style:

```bash
cargo fmt --check
```

- Use `cargo fmt` to apply formatting when editing code.
- Keep `rustfmt.toml` small and consistent with project policy.
- Import-grouping options such as `imports_granularity` and `group_imports` may
  require nightly rustfmt. Do not add them unless the project already accepts
  nightly formatting or explicitly wants that dependency.
- Prefer trailing commas in multiline expressions, match arms, builders, and
  collection literals so future diffs are smaller.

## Clippy

Default command for broad local and CI checks:

```bash
cargo clippy --all-targets --all-features --locked -- -D warnings
```

- `--all-targets` checks libraries, binaries, tests, benches, and examples.
- `--all-features` is useful when features are additive and compatible.
- `--locked` verifies the lockfile is current; drop it only when the project
  intentionally has no lockfile or allows dependency resolution during checks.
- `-D warnings` makes the check fail on warnings without requiring
  `#![deny(warnings)]` in the crate.

Adjust the command when features are mutually exclusive, platform-specific tests
need cfg flags, slow checks are opt-in, or the repository documents a `just`,
`make`, `cargo xtask`, or CI-specific command.

High-value Clippy lints to take seriously:

- `redundant_clone` and `clone_on_copy`.
- `needless_borrow`.
- `needless_collect`.
- `large_enum_variant`.
- `manual_ok_or`.
- `map_unwrap_or`, `unnecessary_map_or`, and
  `unnecessary_result_map_or_else`.
- `unnecessary_wraps`.
- `missing_errors_doc`, `missing_panics_doc`, and `missing_safety_doc` when a
  project opts into stricter public documentation.

Optional lint groups:

- `clippy::pedantic` can be useful for library-quality review, but it contains
  style opinions and false positives.
- `clippy::nursery` can catch emerging patterns, but the lints are less stable.
- Avoid broad `clippy::restriction` adoption unless the repository has a written
  policy. Prefer individual restriction lints when they encode a real local
  constraint.

## Lint Configuration

- Prefer central lint policy over scattered repeated attributes.
- Use Cargo lint tables when available and already accepted by the project:

```toml
[lints.rust]
unsafe_code = "forbid"

[lints.clippy]
redundant_clone = "warn"
needless_collect = "warn"

[workspace.lints.rust]
missing_docs = "warn"

[workspace.lints.clippy]
large_enum_variant = "warn"
```

- Use lint priorities deliberately when a broad group and a specific lint need
  different levels.
- Avoid crate-level `#![deny(warnings)]` in reusable libraries because future
  compiler or Clippy warnings can break downstream builds unexpectedly. Prefer
  CI flags, workspace lint tables, or explicit lint levels.

## Rustdoc Checks

- Run docs when public APIs or examples change:

```bash
cargo doc --no-deps
cargo test --doc
```

- Useful rustdoc lints include `broken_intra_doc_links`, `missing_docs`,
  `empty_docs`, `missing_panics_doc`, `missing_errors_doc`, and
  `missing_safety_doc`.
- Enable stricter documentation lints only when the project is ready to maintain
  them. Apply them at the workspace or crate boundary instead of adding local
  noise.

## CI Integration

- Prefer one documented command per check class: format, lint, test, docs,
  benchmarks when relevant.
- Put common commands in `just`, `make`, `cargo xtask`, or CI scripts when the
  project already uses one of those conventions.
- Keep CI flags and local developer commands close enough that failures can be
  reproduced locally.
- Do not introduce new mandatory tools without documenting installation and
  fallback behavior.

## Benchmarking and Profiling

- Do not claim performance improvements without measurement unless the change
  removes an obvious allocation, clone, lock, or repeated computation.
- Use optimized builds for performance comparisons.
- Use project benchmarks, Criterion, or:

```bash
cargo bench
```

- Use profilers for real workloads, not only micro-benchmarks. Common tools
  include `cargo flamegraph`, `samply`, OS profilers, and application tracing.
- Treat small differences cautiously. Confirm changes exceed normal benchmark
  noise and are relevant to the workload.
- Keep benchmark inputs deterministic and representative.

## Suppression Policy

- Fix warnings before suppressing them.
- Prefer local suppression at the smallest scope.
- Use `#[expect(lint_name, reason = "...")]` where supported so stale
  suppressions are reported.
- Use `#[allow(lint_name)]` only when the lint is knowingly wrong or conflicts
  with a clearer design.
- Do not add broad crate-level allows to make a check pass. If many warnings are
  legitimate for a project, document that policy in central lint config.
