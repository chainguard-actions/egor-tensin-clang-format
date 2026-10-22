<!-- markdownlint-disable -->

# Hardening Report: egor-tensin--clang-format/v1.1.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **egor-tensin--clang-format/v1.1.3** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The composite action's steps pull in other actions by mutable version tags instead of full 40-character commit SHAs: `uses: actions/checkout@v7` (line 22) and `uses: actions/setup-python@v7` (line 29). The checkout step also fetches egor-tensin/cmake-common at the mutable tag `ref: v8.0.2`.

Locations:

- `action.yml:22`
- `action.yml:29`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned actions/checkout@v7 to 3d3c42e5aac5ba805825da76410c181273ba90b1, actions/setup-python@v7 to 6d4fd22b20465ccc0f7354476588e8df7c9ca0e0, and the cmake-common checkout ref v8.0.2 to commit 4464dcbe5f2bff10976d4f4d4d022d98672ecd8e. Each has a trailing comment with its tag name.

