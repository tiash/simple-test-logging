AGENTS.md
=========

General
-------
Follow established patterns and conventions.
Ask Questions, don't assume.

Code Style
----------
- We are using Rust and cargo.
- Use functional patterns where possible.
- No unsafe code if at all possible, if unsafe is necessary the reasons must be clearly documented inline.
- Avoid unnecessary changes, if you identify issues add an entry to `TO-FIX.md`.

Validation
----------
As part of ANY change, all of the following MUST be run and pass before
considering the work complete:
- Formatting: `cargo fmt --all -- --check`
- Lints: `cargo clippy --all-targets --workspace -- -D warnings`
- Tests: `cargo test --workspace`
- Docs: `cargo doc --workspace --no-deps`
- Release build: `cargo build --release`

If any of these fail, fix the issues before finishing. Do not leave the
workspace in a state where any of these checks fail.

Git Hooks
---------
The full validation suite above runs automatically via `pre-commit` and
`pre-push` hooks. The hook scripts live in `.githooks/` (version-controlled)
and are shared across clones via `git config core.hooksPath .githooks`.

One-time setup per fresh clone:
    git config core.hooksPath .githooks

Both hooks run the full 5-check suite (pre-commit AND pre-push). A push
immediately after a clean commit will re-run everything; bypass with
`SKIP=all git push` if you just ran pre-commit.

Bypass individual checks (comma-separated, case-insensitive):
    SKIP=fmt,clippy git commit
    SKIP=test,release git push
    SKIP=all git commit          # skip the entire suite

Valid SKIP names: `fmt`, `clippy`, `test`, `doc`, `release`, `all`.
Hooks are bash 3.2-compatible (stock macOS).

This crate is tiny (a single `init()` helper) with no tests of its own, so
the hooks complete quickly; the release build dominates runtime.
