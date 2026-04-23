# AGENTS.md

This repository publishes a Claude Code plugin and reusable agent skill for Rust
best practices.

## Available Skills

### rust-best-practices

Use when writing, reviewing, refactoring, or explaining Rust code.

The skill covers:

- Ownership, borrowing, cloning, allocation, dispatch, pointers, and shared
  state.
- Public API design, naming, traits, constructors, builders, type safety, and
  feature flags.
- Error handling, panic policy, tests, doc tests, snapshots, comments, and
  rustdoc contracts.
- Tooling with rustfmt, Clippy, Cargo lint tables, rustdoc checks, CI commands,
  benchmark discipline, and profiling.
- Readability, source layout, imports, control flow, helper functions,
  dependency boundaries, and maintainable Rust architecture.

Skill definition: `skills/rust-best-practices/SKILL.md`

## Validation

Before publishing changes, validate:

```sh
python3 -m json.tool .claude-plugin/plugin.json >/dev/null
```

Validate the skill with the Codex skill validator when available:

```sh
uv run --with PyYAML /path/to/skill-creator/scripts/quick_validate.py skills/rust-best-practices
```

When testing in Claude Code, load the plugin locally:

```sh
claude --plugin-dir .
```

## Commit Messages

All commits in this repository should follow Conventional Commits 1.0.0.

Subject format: `<type>[optional scope][!]: <description>`

Allowed types:

| Type | Use for |
|---|---|
| `feat` | New user-visible feature |
| `fix` | Bug fix |
| `docs` | Documentation-only change |
| `style` | Formatting, whitespace; no logic change |
| `refactor` | Internal restructuring; no behavior change |
| `perf` | Performance improvement |
| `test` | Adding or updating tests |
| `build` | Build system, tooling, dependencies |
| `ci` | CI configuration |
| `chore` | Routine maintenance |
| `revert` | Reverts a prior commit |

Breaking changes use `!` after the type or scope and include a
`BREAKING CHANGE:` footer in the body.
