# Brewgen CLI Contract Example

This example keeps coverage on the CLI contract of `brewgen`: help output, version output, and
basic CLI-over-environment precedence.

## Setup

```bash
# should prepare a temp directory for precedence checks
mkdir -p .tmp
```

## Testing

```bash
# should show the debug flag in help output
brewgen --help | grep -- '--debug'

# should show the version flag in help output
brewgen --help | grep -- '--version'

# should show the env-provided brewfile default in help output
BREWGEN_BREWFILE=.tmp/from-env brewgen --help | grep -F -- '.tmp/from-env'

# should not document legacy environment variables
if brewgen --help | grep -F 'TANAAB_'; then exit 1; fi

# should show the invoked command name in usage output
brewgen --help | grep -E 'Usage: .*brewgen '

# should print a version string
test -n "$(brewgen --version)"

# should reject a missing option value
for option in --brewfile --package-type --exclude; do
  if output="$(brewgen "$option" 2>&1)"; then exit 1; fi
  printf '%s\n' "$output" | grep -F -- "$option requires a value."
done

# should reject another option as a value
for option in --brewfile --package-type --exclude; do
  if output="$(brewgen "$option" --force --help 2>&1)"; then exit 1; fi
  printf '%s\n' "$output" | grep -F -- "$option requires a value."
done

# should reject empty option values
for option in --brewfile --package-type --exclude; do
  if output="$(brewgen "$option" '' --help 2>&1)"; then exit 1; fi
  printf '%s\n' "$output" | grep -F -- "$option requires a value."
  if output="$(brewgen "$option=" --help 2>&1)"; then exit 1; fi
  printf '%s\n' "$output" | grep -F -- "$option requires a value."
done

# should accept an equals-delimited filename beginning with a dash
brewgen --brewfile=--force --help | grep -F -- '[default: --force]'

# should accept a filename containing spaces
brewgen --brewfile './Brewfile with spaces' --help | grep -F -- '[default: ./Brewfile with spaces]'

# should let cli brewfile override env brewfile
BREWGEN_BREWFILE=.tmp/from-env \
  brewgen --package-type tap --brewfile .tmp/from-cli --force > .tmp/override.log 2>&1
test -s .tmp/from-cli
test ! -e .tmp/from-env
grep -F 'brewfile generation complete' .tmp/override.log
```
