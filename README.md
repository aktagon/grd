# grd

The coordinator for the `*grd` linter family (ctxgrd, wrkgrd, trtlgrd, aigrd).
grd owns the concerns that span every member, so no single linter has to:

- **`grd stop`** dispatches the Claude Code Stop hook. For the project a turn
  ran in, it asks each `*grd` tool with a config there for its decision, and
  combines any blocks into one.
- **`grd session start`** dispatches the Claude Code SessionStart hook. It
  collects warnings from each member, for example when another live Claude
  session shares the working tree.
- **`grd doctor`** audits how a repository's git hooks compose the family's
  pre-commit fragments, and repairs them with `--fix`.

This repository holds the release binaries and the documentation. The source
is not published.

## Install

```sh
brew install aktagon/tap/grd
```

Or download the archive for your platform from the
[releases page](https://github.com/aktagon/grd/releases) and check it against
`checksums.txt`. Releases cover macOS (Apple silicon and Intel), Linux (x86_64,
statically linked) and Windows (x86_64).

Then wire grd into Claude Code:

```sh
grd doctor                    # report what is missing; writes nothing
grd doctor --fix --harness    # write grd's hook entries and skill into ~/.claude
```

`--fix --harness` backs up `~/.claude/settings.json` before it writes, and
touches only grd's own entries.

## Documentation

- `grd doctor --help`, `grd session --help`
- [Changelog](CHANGELOG.md)

## License

MIT. See [LICENSE](LICENSE).
