# Inputs Example

This example checks the public `brewgen` interface without generating a Brewfile: help, version,
displayed defaults, input validation, and CLI-over-environment precedence.

## Setup

```bash
# should have prepared brewgen on PATH
command -v brewgen >/dev/null
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

# should show the default output filename
brewgen --help | grep -F -- '[default: Brewfile]'

# should show the default package selection
brewgen --help | grep -F -- '[default: tap,cask,brew]'

# should let cli brewfile override env brewfile
BREWGEN_BREWFILE=Brewfile.env brewgen --brewfile Brewfile.cli --help | grep -F -- '[default: Brewfile.cli]'

# should accept comma-separated package types from the environment
BREWGEN_PACKAGE_TYPES=brew,tap brewgen --help | grep -F -- '[default: brew,tap]'

# should let repeated package-type options replace environment defaults
BREWGEN_PACKAGE_TYPES=cask brewgen --package-type brew --package-type tap --help | grep -F -- '[default: brew,tap]'

# should accept comma-separated exclusions from the environment
BREWGEN_EXCLUDE=codex,git brewgen --help | grep -F -- '[default: codex,git]'

# should let repeated exclude options replace environment defaults
BREWGEN_EXCLUDE=env-package brewgen --exclude codex --exclude git --help | grep -F -- '[default: codex,git]'
```
