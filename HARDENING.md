<!-- markdownlint-disable -->

# Hardening Report: egor-tensin--clang-format/v1.1.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **egor-tensin--clang-format/v1.1.2** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Two composite action steps use mutable version tags instead of pinned full 40-character SHA commit digests, making the action vulnerable to supply-chain attacks if the upstream tag is moved or hijacked.
- `uses: actions/checkout@v7` (line 22)
- `uses: actions/setup-python@v7` (line 28)

Locations:

- `action.yml:22`
- `action.yml:28`

### script-injection (severity: high)

Rule (b) violation: The `run:` block (PowerShell) expands env vars that hold workflow-controllable input values without quoting them in the command invocation.

- `$style` (sourced from `$env:ARG_STYLE` = `${{ inputs.style }}`) is passed unquoted to the `python` command: `--style $style`
- `$clang_format` (derived from `$env:ARG_VERSION` = `${{ inputs.version }}`) is passed unquoted: `--clang-format $clang_format`
- `$version` (same source) is interpolated unquoted inside a string: `$clang_format += "-$version"`

In PowerShell, unquoted variable expansion in argument position allows special characters (spaces, semicolons, etc.) from attacker-controlled inputs to alter the command structure. These variables should be quoted (e.g., `"$style"`, `"$clang_format"`).

Locations:

- `action.yml:44`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection

**Notes:**

1. Pinned actions/checkout@v7 to SHA 3d3c42e5aac5ba805825da76410c181273ba90b1 and actions/setup-python@v7 to SHA 5fda3b95a4ea91299a34e894583c3862153e4b97, preserving the version tags as comments.
2. Fixed PowerShell script-injection by quoting $clang_format and $style with double quotes in the python command invocation (--clang-format "$clang_format" --style "$style"), and wrapped $version in a subexpression $($version) in the string concatenation to prevent special characters from altering command structure.

