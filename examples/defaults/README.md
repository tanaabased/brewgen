# Defaults Example

This example runs `brewgen` without options in an isolated working directory. It verifies the
default `./Brewfile` destination and compares the default tap, cask, and formula entries with
Homebrew's own dump, allowing any category to be empty on the runner.

## Setup

```bash
# should capture the installed packages in the default categories
for section in tap cask formula; do
  brew bundle dump --file "$TMPDIR/expected.$section" --force "--$section"
done
```

## Testing

```bash
# should generate a Brewfile with no options
test ! -e "$TMPDIR/Brewfile"
(cd "$TMPDIR" && brewgen) 2>&1 | tee "$TMPDIR/run.log"

# should report successful generation
grep -F 'brewfile generation complete' "$TMPDIR/run.log"

# should write the default filename in the working directory
test -s "$TMPDIR/Brewfile"

# should include the installed packages from every default category
awk '/^(tap|cask|brew) "/' "$TMPDIR/expected.tap" "$TMPDIR/expected.cask" "$TMPDIR/expected.formula" | LC_ALL=C sort > "$TMPDIR/expected.entries"
awk '/^(tap|cask|brew) "/' "$TMPDIR/Brewfile" | LC_ALL=C sort > "$TMPDIR/actual.entries"
cmp "$TMPDIR/expected.entries" "$TMPDIR/actual.entries"

# should omit non-default package categories
! grep -Eq '^(mas|vscode|go|cargo|uv|flatpak) "' "$TMPDIR/Brewfile"
```
