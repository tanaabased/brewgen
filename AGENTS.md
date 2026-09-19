# Repo Guidance For `brewgen`

## Purpose

`brewgen` is a hosted Bash script that generates a Brewfile from the invoking machine's Homebrew
state. It supports Bash on macOS with Homebrew and `brew bundle` available.

## Source Map

- `brewgen.sh`: source CLI contract.
- `README.md`: hosted and installed user journeys.
- `examples/**/README.md`: executable Leia scenarios run on macOS CI.
- `site/llms.txt` and `scripts/build-dist.js`: hosted metadata and distribution inputs.
- `dist/`: generated, release-owned Netlify output.

## Critical Rules

- Preserve the single top-level `SCRIPT_VERSION` assignment in `brewgen.sh`; release automation
  stamps the generated entrypoint in place.
- Treat `BREWGEN_*` as the public environment-variable namespace. Keep `TANAAB_*` only as an
  undocumented compatibility fallback covered by the legacy scenario.
- Keep the CLI non-interactive: it neither prompts nor invokes sudo. Do not import Bootbox
  `/dev/tty` or sudo behavior without a new Brewgen interaction contract.
- Do not edit, regenerate, stage, or commit `dist/` during routine work. Update its source inputs
  and leave generated release changes to CI unless release-shaped verification is explicitly
  requested.
- Keep `site/llms.txt`, `README.md`, `brewgen.sh --help`, and affected Leia examples aligned when
  the public contract changes.

## Validation

- Use `bun run lint` and `git diff --check` for routine changes.
- Treat Homebrew-backed Leia scenarios as CI-owned unless a requested change is explicitly
  non-mutating and locally safe to exercise.
