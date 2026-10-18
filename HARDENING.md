<!-- markdownlint-disable -->

# Hardening Report: egor-tensin--clang-format/v1.1.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **egor-tensin--clang-format/v1.1.3** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Two `uses:` references in action.yml use mutable version tags instead of full 40-character SHA commit hashes. This means a compromised or updated upstream action could silently change the code executed by this action, enabling a supply-chain attack.

Failing references:
- `uses: actions/checkout@v7` (line 22) — should be pinned to a full SHA, e.g. `actions/checkout@<40-char-sha> # v4`
- `uses: actions/setup-python@v7` (line 28) — should be pinned to a full SHA, e.g. `actions/setup-python@<40-char-sha> # v5`

Locations:

- `action.yml:22`
- `action.yml:28`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned both unpinned `uses:` references in hardened/action/action.yml to their full 40-character SHA commit hashes:
- `actions/checkout@v7` → `actions/checkout@3d3c42e5aac5ba805825da76410c181273ba90b1 # v7`
- `actions/setup-python@v7` → `actions/setup-python@5fda3b95a4ea91299a34e894583c3862153e4b97 # v7`
Original version tags are preserved as inline comments for readability.

