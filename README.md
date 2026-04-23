# rust-best-practices

Claude Code plugin and reusable agent skill for writing and reviewing idiomatic
Rust code.

The skill is source-neutral: it applies Rust ecosystem guidance for new and
existing Rust projects, including ownership, borrowing, API design, error
handling, tests, rustdoc, Clippy, rustfmt, performance, unsafe code review, and
maintainable project structure.

## Skills

| Skill | Description |
|---|---|
| `rust-best-practices` | Review, write, and refactor Rust code for readability, API design, ownership, errors, tests, docs, tooling, and performance. |

## Installation

### Claude Code

After the marketplace is published:

```text
/plugin marketplace add tailrocks/tailrocks-marketplace
/plugin install rust-best-practices@tailrocks-marketplace
```

For local development:

```sh
cd rust-best-practices
claude --plugin-dir .
```

Then use the namespaced skill in Claude Code:

```text
/rust-best-practices:rust-best-practices review this crate
```

### Codex

See [.codex/INSTALL.md](.codex/INSTALL.md).

## Repository Layout

```text
rust-best-practices/
|-- .claude-plugin/
|   `-- plugin.json
|-- skills/
|   `-- rust-best-practices/
|       |-- SKILL.md
|       |-- agents/
|       `-- references/
|-- .codex/
|   `-- INSTALL.md
|-- AGENTS.md
|-- CLAUDE.md
|-- LICENSE
`-- README.md
```

## License

Apache-2.0
