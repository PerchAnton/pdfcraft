# Repository checks

Run `cargo xtask` for the available commands. The vendor ledger check is:

```sh
cargo test --locked -p xtask vendor_ledger
cargo run --locked -p xtask -- vendor-ledger
```

Add `--list` after `vendor-ledger` to print each citation's classification.

The checker compares the versions and patch entries in `vendor/README.md`
with the patched crate manifests and source markers. Every ledger row has
five columns: crate, version, licence, patches, and regression tests. Keep
test citations in their own column when consolidating duplicate rows.

Citation names are indexed across `vendor/`, `apps/pdfcraft/`, and `crates/`.
A file-only citation such as `lib.rs` requires an indexed file. A qualified
citation such as `lib.rs::tests::replacement` also requires its final symbol.
This is a lightweight name index, not a Rust parser: it does not establish
the full module path, require a `#[test]` attribute, or prove that a cited
test ran. Vendored test execution is a separate workflow.

The scanner reads ASCII identifiers at the keyword boundary, ignores function
pointer syntax, and checks the immediate function parameter list, allowing
up to 64 characters of balanced generics. It tolerates Unicode surrounding
text without slicing inside a character. Patch-marker warnings use the
highest explicit `(n)` number from 1 through 500, including numbers after a
gap; prose such as `(4 GiB)` is not a patch number. Gaps are not filled in.

`vendor-ledger.yml` follows all indexed source trees and the checker/build
inputs. It sets up stable Rust, uses a dedicated build cache, and retains
the five-minute job limit. `vendor-patches.yml` runs the larger vendored
test/compile matrix separately, so a workspace-only test rename triggers
the ledger check without rebuilding that matrix. Both workflows retain
their advisory status (`continue-on-error`) pending gate graduation.
