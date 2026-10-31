# Changelog

All notable changes to grd are documented in this file. The format follows
[Keep a Changelog](https://keepachangelog.com/), and the project adheres to
[Semantic Versioning](https://semver.org/). wrkgrd's pre-push release guard
checks each release tag: its version must match `Cargo.toml`, this changelog
must carry an entry for it, and the tree must be clean.

## [Unreleased]

## [0.3.0] - 2026-10-31

### Added

- **One skip-when-unchanged for the family** (`ADR-010`). The workspace crate
  `crates/grd-skip` keys a member's run on the working tree, its config and
  the arguments that change the result, and stores the clean run's output.
  On an unchanged tree a member prints that output again, then
  `<member>: skipped: unchanged since clean run at <UTC>, key <7 hex>`. A
  member that cannot skip prints `<member>: not skipped: <reason>` on stderr.
  wrkgrd, ctxgrd and trtlgrd build against it by path, at `../grd`. The grd
  binary does not link it.

### Changed

- **`grd stop` runs trtlgrd as `trtlgrd lint --harness claude`** (`ADR-010`
  Open Question 6), in place of `lint --format json`. trtlgrd's skip line
  now reaches the `systemMessage`.

### Not shipped

- No `x86_64-pc-windows-msvc` archive. This release was built by hand on a
  Mac, which cannot build it.

## [0.2.0] - 2026-10-21

### Added

- **Public release binaries and a Homebrew formula.** `brew install
  aktagon/tap/grd` installs a prebuilt binary for macOS (Apple silicon, Intel)
  and Linux (x86_64, statically linked). The archives and `checksums.txt` are
  attached to the releases on the public repository, `aktagon/grd`, which
  holds the binaries and the documentation. The source stays private.

- **Generated wire types** (`ADR-003`). `codegen/generate.py` writes the
  family's Rust types from `docs/schema/grd-output.schema.json`; `make
  codegen` regenerates grd's copy and `cargo test` fails when it is stale.
  Members run it with `uv run $(GRD_CHECKOUT)/codegen/generate.py`.

### Changed

- **The schema requires `line` and `col`** on every diagnostic (GRD-066).
  Every member already printed them, as `null` when unknown.
- `grd doctor --format json` and `grd stop`'s diagnostic count use the
  generated types. A member whose exit-1 output does not match the schema is
  reported as "some diagnostic(s)" instead of a count of its JSON items.

### Removed

- The Python `bin/` scripts, the unittest suite and the differential run
  (`ADR-008` GRD-064). `make test` runs `cargo test`.

### Changed

- The grd skill names the Rust dispatch tables (`src/stop.rs`,
  `src/session.rs`); its `VERSION` is 2.

## [0.1.0] - 2026-10-17

### Added

- **One Rust `grd` binary** (`ADR-008`). `grd doctor`, `grd stop` and
  `grd session start` are subcommands of it. It embeds every file under
  `share/grd/`, so it runs with no data files beside it (GRD-061).
- **Release archives** for `x86_64-unknown-linux-musl`, `x86_64-apple-darwin`,
  `aarch64-apple-darwin` and `x86_64-pc-windows-msvc`, with `checksums.txt`
  (GRD-060). They are published on this repo, which is private.
- **`make differential`** runs the Python and the Rust grd on the same
  fixtures and reports every difference (GRD-062).
- MIT license.

### Changed

- **`make install` installs only the Rust `grd`** (GRD-064). It removes the
  Python install's `grd-stop`, `grd-session` and `~/.local/share/grd/` files.
  The Stop and SessionStart hook entries in `settings.json` need no change.
- Where the Python grd raised on malformed member output or a settings.json
  that is a JSON list, grd now fails open and treats the field as absent.
- The `grd doctor --fix --harness` settings backup stamp is computed in UTC.
