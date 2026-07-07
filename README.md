# simple-test-logging

A tiny wrapper around [`stderrlog`] that makes it easy to initialize logging
from integration tests, guarding against double-init panics when tests run in
parallel within the same binary.

[`stderrlog`]: https://docs.rs/stderrlog

## Usage

Add it as a dev-dependency:

```toml
[dev-dependencies]
simple-test-logging = { git = "https://github.com/tiash/simple-test-logging.git" }
```

Then call `simple_test_logging::init()` at the top of every test:

```rust
#[tokio::test]
async fn something() {
    simple_test_logging::init();
    // ...
}
```

## What it does

- The first `init()` call in a process installs a global `stderrlog` logger
  wrapped in a filter that drops noisy messages from the `serial_test` crate.
- Subsequent calls are no-ops (guarded by a `std::sync::Once`), so parallel
  tests in the same binary never hit `SetLoggerError` panics.

## Log level

The level is read once, at the first `init()` call, from the `LOG_LEVEL`
environment variable (parsed as a [`log::LevelFilter`]):

| Value (case-insensitive) | Meaning                |
| ------------------------ | ---------------------- |
| `off`                    | logging disabled       |
| `error` (default)        | only errors            |
| `warn`                   | warnings + errors      |
| `info`                   | info and above         |
| `debug`                  | debug and above        |
| `trace`                  | everything             |

When unset or invalid it defaults to `error` to keep test output quiet. To
debug a single test run, override it per invocation, e.g.:

```sh
LOG_LEVEL=debug cargo test -p my-crate
```

[`log::LevelFilter`]: https://docs.rs/log/latest/log/enum.LevelFilter.html
