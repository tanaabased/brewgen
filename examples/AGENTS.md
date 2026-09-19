# Leia Example Guidance

- `inputs` owns non-mutating help, version, validation, and public input-precedence checks.
- `defaults` owns an option-free run with the default output path and package selection.
- `brewfiles` owns generation, filtering, output paths, equivalent CLI/environment results, and
  overwrite protection.
- `legacy` owns `TANAAB_*` compatibility and preference for the public `BREWGEN_*` namespace.
- Use `# <Domain> Example`, `## Setup`, and `## Testing`, with one observable behavior per
  blank-line-separated `# should ...` block. Shell variables do not persist between blocks.
- Keep runtime-derived inputs and outputs under the workflow-provided `TMPDIR`. Capture output
  when multiple assertions need it; keep diagnostics visible in CI.
- Test the prepared `brewgen` artifact on `PATH`. Generation scenarios remain CI-owned;
  non-mutating `inputs` and `legacy` checks may run locally.
- Keep stdin closed and disable retries for CI scenarios so partial file mutations fail visibly.
- Update the explicit workflow matrix when scenario names change. Add a scenario only when an
  existing behavior domain cannot own it coherently.
