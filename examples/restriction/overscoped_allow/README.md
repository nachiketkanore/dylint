# overscoped_allow

### What it does
Checks for `allow` attributes whose scope could be reduced.

### Why is this bad?
An `allow` attribute whose scope is too large could suppress warnings/errors and cause them
to go unnoticed.

### Known problems
Recommends to reduce to item, `Impl` item, and statement scopes only (not arbitrary inner
scopes).

### How to use this lint
Two steps are required:
1. For the lint whose `allow` scopes you want to check, run it at the [`force-warn`] level
   and store the resulting warnings in a file called `warnings.json`. For example, to check
   the scopes of `allow(clippy::unwrap_used)`, you might run the following command:
   ```sh
   cargo clippy --message-format=json -- --force-warn clippy::unwrap-used > warnings.json
   ```
   To perform a similar check for the Dylint lint `non_thread_safe_call_in_test`, you might
   run the following command:
   ```sh
   DYLINT_RUSTFLAGS='--force-warn non_thread_safe_call_in_test' cargo dylint \
      --lib non_thread_safe_call_in_test -- --message-format=json > warnings.json
   ```
2. Run the `overscoped_allow` lint. The lint will find and use the `warnings.json` file
   generated in 1.

To use a file other than `warnings.json`, store that file's path in the environment variable
variable `OVERSCOPED_ALLOW_PATH`.

### Example
```rust
#[allow(clippy::module_name_repetitions)]
mod cake {
    struct BlackForestCake;
}
```
Use instead:
```rust
mod cake {
    #[allow(clippy::module_name_repetitions)]
    struct BlackForestCake;
}
```

[`force-warn`]: https://doc.rust-lang.org/rustc/lints/levels.html#force-warn