# Brewgen CLI Contract Example

This example keeps coverage on the CLI contract of `brewgen.sh`: help output, version output, and
basic CLI-over-environment precedence.

## Setup

```bash
# should prepare a temp directory for precedence checks
mkdir -p .tmp
```

## Testing

```bash
# should show the debug flag in help output
brewgen.sh --help | grep -- '--debug'
# should show the version flag in help output
brewgen.sh --help | grep -- '--version'
# should show the env-provided brewfile default in help output
TANAAB_BREWFILE=.tmp/from-env brewgen.sh --help | grep -F -- '.tmp/from-env'
# should print a version string
test -n "$(brewgen.sh --version)"
# should let cli brewfile override env brewfile
TANAAB_BREWFILE=.tmp/from-env \
  brewgen.sh --package-type tap --brewfile .tmp/from-cli --force > .tmp/override.log 2>&1
test -s .tmp/from-cli
test ! -e .tmp/from-env
grep -F 'brewfile generation complete' .tmp/override.log
```
