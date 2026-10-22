<!-- markdownlint-disable -->

# Hardening Report: egor-tensin--clang-format/v1.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **egor-tensin--clang-format/v1.1.0** was hardened automatically. 5 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The composite action references actions by tag instead of a full commit SHA: 'uses: actions/checkout@v7' (it also checks out egor-tensin/cmake-common at the mutable ref v8.0.2) and 'uses: actions/setup-python@v7'.

Locations:

- `action.yml:23`
- `action.yml:29`

### script-injection (severity: high)

Sub-rule (a): expressions are placed directly inside the pwsh run: block of step 'run'. The lines are: New-Variable version -Value '${{ inputs.version }}'; New-Variable style -Value '${{ inputs.style }}'; New-Variable exclude -Value '${{ inputs.exclude }}'; $env:PYTHONPATH = Join-Path '${{ github.workspace }}' 'cmake'. A single quote in an input value ends the PowerShell string and lets an attacker run arbitrary code. Fix: pass the values through env: (e.g. INPUT_VERSION) and read them as $env:INPUT_VERSION. Use $env:GITHUB_WORKSPACE for the workspace path.

Locations:

- `action.yml:35`
- `action.yml:36`
- `action.yml:37`
- `action.yml:49`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.version }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:35`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.style }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:36`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.exclude }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:37`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, static-inline-injection

**Notes:**

Pinned actions/checkout@v7 to 3d3c42e5aac5ba805825da76410c181273ba90b1 and actions/setup-python@v7 to 6d4fd22b20465ccc0f7354476588e8df7c9ca0e0. Pinned the egor-tensin/cmake-common ref v8.0.2 to 4464dcbe5f2bff10976d4f4d4d022d98672ecd8e. In the pwsh step, the inputs version, style and exclude now go through env (INPUT_*). The script reads them as [string]$env:INPUT_*. The [string] cast turns a missing value into an empty string, so .Split and the empty-version check still work. github.workspace is replaced with $env:GITHUB_WORKSPACE. Not tested.

