# brewgen

`brewgen` is the hosted script repo for Tanaab based Brewfile generation. It inspects the current
Homebrew state and writes a `Brewfile`, with options to limit package types, exclude packages, and
choose the output path. Under the hood it uses `brew bundle dump` and assembles the selected
sections into one output file.

> Runtime support: Bash on macOS.

## Quickstart

```sh
curl -fsSL https://brewgen.tanaab.sh/brewgen.sh | bash
```

## Installation

`brewgen` is designed around the hosted raw script at `https://brewgen.tanaab.sh/brewgen.sh`.

- The supported runtime is Bash on macOS.
- The hosted URL serves the generated `dist/brewgen.sh` entrypoint used for release-shaped
  validation and Netlify publishing.

## Usage

By default, `brewgen.sh` writes a `Brewfile` in the current directory from the local Homebrew
state. The default package types are `tap`, `cask`, and `brew`, and the script will refuse to
overwrite an existing file unless you pass `--force`.

```sh
brewgen.sh
brewgen.sh --help
brewgen.sh --version
brewgen.sh --brewfile ./Brewfile.work --force
brewgen.sh --package-type tap --package-type brew
brewgen.sh --exclude codex --exclude visual-studio-code
TANAAB_DEBUG=1 brewgen.sh --package-type cask
```

If you are working from a local checkout instead of a hosted URL, replace `brewgen.sh` with
`./brewgen.sh`.

`brewgen.sh` expects a working Homebrew installation and `brew bundle` support on the current
machine.

The [`examples/`](/Users/pirog/tanaab/brewgen/examples) directory contains Leia-tested usage
examples that run on every pull request.

## Common Patterns

```sh
# write the default Brewfile in the current directory
brewgen.sh

# write to a different file and allow overwrite
brewgen.sh --brewfile ./Brewfile.work --force

# generate only taps and formulae
brewgen.sh --package-type tap --package-type brew

# exclude specific packages from the final output
brewgen.sh --exclude codex --exclude visual-studio-code
```

## Options

- `--brewfile <path>` writes the generated Brewfile to the chosen path. Parent directories are
  created when needed.
- `--package-type <type>` limits generation to one or more package types. Supported values are
  `tap`, `brew` or `formula`, `cask`, `mas`, `vscode`, `go`, `cargo`, `uv`, and `flatpak`. Repeat
  the flag to include multiple sections.
- `--exclude <name>` removes matching package names from the final output. Repeat it to exclude
  multiple packages. Matching is by exact package name in the generated Brewfile entries.
- `--force` allows overwriting an existing output file.
- `--debug` turns on debug logging and enables Homebrew debug mode for the current run.
- `--version` and `--help` print metadata and usage.

CLI options override environment variables, and environment variables override built-in defaults.

## Environment Variables

- `TANAAB_BREWFILE` sets the output Brewfile path.
- `TANAAB_PACKAGE_TYPES` sets a comma-separated default list of package types.
- `TANAAB_EXCLUDE` sets a comma-separated default list of package names to exclude.
- `TANAAB_FORCE` enables overwrite behavior when set to a truthy value.
- `TANAAB_DEBUG` enables debug logging when set to a truthy value.

## Advanced

If you want a reusable local command instead of piping the hosted script every time, install it
into a directory that is already in your `PATH` or one you manage yourself.

```sh
mkdir -p "$HOME/.local/bin"
curl -fsSL https://brewgen.tanaab.sh/brewgen.sh -o "$HOME/.local/bin/brewgen"
chmod +x "$HOME/.local/bin/brewgen"

brewgen --help
brewgen --version
```

## Development

`brewgen` uses Bun for repo-local tooling and treats `dist/` as a tracked, Netlify-ready release
surface.

```sh
bun install
bun run lint
bun run build
```

The `examples/` directory exists as executable scenario coverage, and those scenarios are exercised
in CI with Leia on macOS against the prepared `dist/` artifact.

## Issues, Questions and Support

Use the [GitHub issue queue](https://github.com/tanaabased/brewgen/issues) for bugs, regressions,
or feature requests.

## Changelog

See [`CHANGELOG.md`](./CHANGELOG.md) for release history and
[GitHub releases](https://github.com/tanaabased/brewgen/releases) for published artifacts.

## Maintainers

- `@pirog`

## Contributors

<a href="https://github.com/tanaabased/brewgen/graphs/contributors">
  <img src="https://contrib.rocks/image?repo=tanaabased/brewgen" />
</a>

Made with [contrib.rocks](https://contrib.rocks).
