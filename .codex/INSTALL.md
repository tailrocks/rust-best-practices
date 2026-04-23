# Installing rust-best-practices for Codex

Codex can use the bundled skill directly from this repository.

## Option 1: Reference from a Project

Clone the repository alongside the target Rust project:

```sh
git clone https://github.com/tailrocks/rust-best-practices.git
```

Add this note to the target project's `AGENTS.md`:

```markdown
## Rust Best Practices

Rust best-practices guidance is available in
`../rust-best-practices/skills/rust-best-practices/`. When asked to write,
review, or refactor Rust code, read and follow that `SKILL.md`.
```

## Option 2: Symlink into Codex Skills

```sh
mkdir -p .codex/skills
ln -s /path/to/rust-best-practices/skills/rust-best-practices .codex/skills/rust-best-practices
```

## Option 3: Copy into Codex Home

```sh
mkdir -p "${CODEX_HOME:-$HOME/.codex}/skills"
cp -R /path/to/rust-best-practices/skills/rust-best-practices "${CODEX_HOME:-$HOME/.codex}/skills/"
```
