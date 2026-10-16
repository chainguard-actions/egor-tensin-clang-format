<!-- markdownlint-disable -->

# Hardening Report: egor-tensin--clang-format/v1.1.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **egor-tensin--clang-format/v1.1.1** was hardened automatically. 1 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Two `uses:` references in action.yml use mutable tag-based refs instead of pinned 40-character SHA digests, making the action vulnerable to supply-chain attacks if the upstream tags are moved or overwritten:
- `uses: actions/checkout@v7` (line 22) — should be pinned to a full commit SHA
- `uses: actions/setup-python@v7` (line 28) — should be pinned to a full commit SHA

Locations:

- `action.yml:22`
- `action.yml:28`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned both unpinned `uses:` references in hardened/action/action.yml to their full 40-character commit SHAs:
- `actions/checkout@v7` → `actions/checkout@3d3c42e5aac5ba805825da76410c181273ba90b1 # v7` (line 22)
- `actions/setup-python@v7` → `actions/setup-python@5fda3b95a4ea91299a34e894583c3862153e4b97 # v7` (line 28)
Original tag names preserved as inline comments for readability.

### Iteration 2

**Fixes applied:** script-injection

**Notes:**

Fixed script injection in action.yml line 49: quoted the PowerShell variables `$clang_format` and `$style` in the external command call. Changed `& python -m project.format --clang-format $clang_format --style $style $exclude_list` to `& python -m project.format --clang-format "$clang_format" --style "$style" $exclude_list`. The `$exclude_list` variable is intentionally left unquoted as it is an array that needs to be splatted as multiple arguments. The `inputs.version` and `inputs.style` values are now properly quoted to prevent word-splitting and argument injection.

