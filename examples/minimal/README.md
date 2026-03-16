# Brewgen Minimal Example

This example is the smallest markdown-first smoke test for `brewgen.sh`. It runs a real Brewfile
generation flow from the prepared `dist/brewgen.sh` artifact and verifies that a Brewfile was
written.

## Setup

```bash
# should generate a Brewfile with tap entries
mkdir -p .tmp
brewgen.sh --package-type tap --brewfile .tmp/Brewfile.generated --force > .tmp/run.log 2>&1
```

## Testing

```bash
# should report successful generation
grep -F 'brewfile generation complete' .tmp/run.log

# should write the requested Brewfile
test -s .tmp/Brewfile.generated

# should include at least one tap entry
grep -E '^tap "' .tmp/Brewfile.generated
```
