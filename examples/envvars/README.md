# Brewgen Environment Variables Example

This example exercises `brewgen` with environment variables only. It discovers installed formulae
from the current Homebrew state, generates a Brewfile in a nested output directory, and verifies
the resulting file content.

## Setup

```bash
# should discover installed brew packages for the example
mkdir -p .tmp/out
brewgen --package-type brew --brewfile .tmp/discovery.Brewfile --force > .tmp/discovery.log 2>&1
grep -E '^brew "' .tmp/discovery.Brewfile | cut -d'"' -f2 > .tmp/formulae
sed -n '1p' .tmp/formulae > .tmp/exclude-name
sed -n '2p' .tmp/formulae > .tmp/include-name
test -s .tmp/exclude-name
test -s .tmp/include-name

# should generate a filtered brew only Brewfile
exclude_name="$(cat .tmp/exclude-name)"
TANAAB_PACKAGE_TYPES=brew \
TANAAB_EXCLUDE="$exclude_name" \
TANAAB_BREWFILE=.tmp/out/Brewfile.generated \
TANAAB_FORCE=1 \
  brewgen > .tmp/run.log 2>&1
```

## Testing

```bash
# should report successful generation
grep -F 'brewfile generation complete' .tmp/run.log

# should write the requested Brewfile
test -s .tmp/out/Brewfile.generated

# should create parent directories for the output path
test -d .tmp/out

# should include only brew entries
grep -Eq '^brew "' .tmp/out/Brewfile.generated
! grep -Eq '^(tap|cask|mas|vscode|go|cargo|uv|flatpak) "' .tmp/out/Brewfile.generated

# should exclude the requested package
excluded="$(cat .tmp/exclude-name)"
! grep -F "brew \"$excluded\"" .tmp/out/Brewfile.generated

# should keep other brew packages
included="$(cat .tmp/include-name)"
grep -F "brew \"$included\"" .tmp/out/Brewfile.generated
```
