# Legacy Example

This scenario preserves transition support for the former `TANAAB_*` environment namespace. New
callers use `BREWGEN_*`; legacy names remain an implementation compatibility path, not the public
contract.

## Setup

```bash
# should have prepared brewgen on PATH
command -v brewgen >/dev/null
```

## Testing

```bash
# should accept a legacy brewfile default
TANAAB_BREWFILE=.tmp/from-legacy brewgen --help | grep -F -- '.tmp/from-legacy'

# should prefer a Brewgen brewfile default over the legacy value
BREWGEN_BREWFILE=.tmp/from-brewgen TANAAB_BREWFILE=.tmp/from-legacy \
  brewgen --help | grep -F -- '.tmp/from-brewgen'
if BREWGEN_BREWFILE=.tmp/from-brewgen TANAAB_BREWFILE=.tmp/from-legacy \
  brewgen --help | grep -F -- '.tmp/from-legacy'; then exit 1; fi

# should retain legacy package-type defaults
TANAAB_PACKAGE_TYPES=brew TANAAB_EXCLUDE=codex \
  brewgen --help | grep -F -- '[default: brew]'

# should retain legacy exclude defaults
TANAAB_PACKAGE_TYPES=brew TANAAB_EXCLUDE=codex \
  brewgen --help | grep -F -- '[default: codex]'
```
