# Brewfiles Example

This example generates filtered Brewfiles through CLI options and environment variables, then
compares the outputs. It covers package selection, exclusions, nested output directories,
CLI output-path precedence, and overwrite protection. Non-mutating input checks live in `inputs`.

## Setup

```bash
# should discover installed formulae for filtering
brewgen --package-type brew --brewfile "$TMPDIR/discovery.Brewfile" --force
grep -E '^brew "' "$TMPDIR/discovery.Brewfile" | cut -d'"' -f2 > "$TMPDIR/formulae"
sed -n '1p' "$TMPDIR/formulae" > "$TMPDIR/exclude-name"
sed -n '2p' "$TMPDIR/formulae" > "$TMPDIR/include-name"
test -s "$TMPDIR/exclude-name"
test -s "$TMPDIR/include-name"
```

## Testing

```bash
# should generate a filtered Brewfile through CLI options
exclude_name="$(cat "$TMPDIR/exclude-name")"
test ! -e "$TMPDIR/cli"
BREWGEN_BREWFILE="$TMPDIR/from-env" brewgen \
  --package-type brew \
  --exclude "$exclude_name" \
  --brewfile "$TMPDIR/cli/Brewfile" \
  --force 2>&1 | tee "$TMPDIR/cli.log"

# should generate a filtered Brewfile through environment variables
exclude_name="$(cat "$TMPDIR/exclude-name")"
test ! -e "$TMPDIR/env"
BREWGEN_PACKAGE_TYPES=brew \
BREWGEN_EXCLUDE="$exclude_name" \
BREWGEN_BREWFILE="$TMPDIR/env/Brewfile" \
BREWGEN_FORCE=1 \
  brewgen 2>&1 | tee "$TMPDIR/env.log"

# should report successful generation for both input routes
grep -F 'brewfile generation complete' "$TMPDIR/cli.log"
grep -F 'brewfile generation complete' "$TMPDIR/env.log"

# should create both requested output directories
test -d "$TMPDIR/cli"
test -d "$TMPDIR/env"

# should write equivalent Brewfiles for both input routes
test -s "$TMPDIR/cli/Brewfile"
test -s "$TMPDIR/env/Brewfile"
cmp "$TMPDIR/cli/Brewfile" "$TMPDIR/env/Brewfile"

# should leave the overridden environment output path unused
test ! -e "$TMPDIR/from-env"

# should include only formula entries
grep -Eq '^brew "' "$TMPDIR/cli/Brewfile"
! grep -Eq '^(tap|cask|mas|vscode|go|cargo|uv|flatpak) "' "$TMPDIR/cli/Brewfile"

# should exclude the requested package
excluded="$(cat "$TMPDIR/exclude-name")"
! grep -F "brew \"$excluded\"" "$TMPDIR/cli/Brewfile"

# should keep other formulae
included="$(cat "$TMPDIR/include-name")"
grep -F "brew \"$included\"" "$TMPDIR/cli/Brewfile"

# should preserve an existing Brewfile without force
cp "$TMPDIR/cli/Brewfile" "$TMPDIR/original.Brewfile"
if output="$(brewgen --package-type tap --brewfile "$TMPDIR/cli/Brewfile" 2>&1)"; then exit 1; fi
printf '%s\n' "$output" | grep -F 'already exists'
cmp "$TMPDIR/original.Brewfile" "$TMPDIR/cli/Brewfile"
```
