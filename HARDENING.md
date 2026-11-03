<!-- markdownlint-disable -->

# Hardening Report: egor-tensin--clang-format/v1.1.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **egor-tensin--clang-format/v1.1.1** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The composite action references two actions by tag instead of by full 40-character commit SHA: 'uses: actions/checkout@v7' and 'uses: actions/setup-python@v7'.

Locations:

- `action.yml:23`
- `action.yml:30`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Pinned actions/checkout@v7 to 3d3c42e5aac5ba805825da76410c181273ba90b1 and actions/setup-python@v7 to 6d4fd22b20465ccc0f7354476588e8df7c9ca0e0, keeping the tag as a trailing comment. Pre-existing upstream quirk left untouched: the pwsh step reads $env:VERSION while env defines ARG_VERSION (so the version input is ignored); not a security finding.

