<!-- markdownlint-disable -->

# Hardening Report: egor-tensin--clang-format/v1.0.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **egor-tensin--clang-format/v1.0.3** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): The `run:` block in action.yml directly interpolates GitHub Actions expressions inside the PowerShell script string. Specifically, `${{ inputs.version }}`, `${{ inputs.style }}`, `${{ inputs.exclude }}`, and `${{ github.workspace }}` are embedded directly in the shell command. An attacker-controlled value for any of these inputs (e.g. `inputs.version`, `inputs.style`, `inputs.exclude`) could inject arbitrary PowerShell commands. These values should be passed via environment variables and referenced as `$env:VAR_NAME` instead.

Locations:

- `action.yml:23`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.version }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:25`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.style }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:26`

### static-inline-injection (severity: high)

shell injection: expression "${{ inputs.exclude }}" appears directly in run: block of step ""; move to env: map

Locations:

- `action.yml:27`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, static-inline-injection

**Notes:**

Moved all GitHub Actions expressions out of the PowerShell run: block and into an env: block. Specifically: ${{ inputs.version }} → env.INPUT_VERSION, ${{ inputs.style }} → env.INPUT_STYLE, ${{ inputs.exclude }} → env.INPUT_EXCLUDE, and ${{ github.workspace }} → env.INPUT_WORKSPACE. The PowerShell script now references these via $env:INPUT_VERSION, $env:INPUT_STYLE, $env:INPUT_EXCLUDE, and $env:INPUT_WORKSPACE respectively, preventing attacker-controlled values from being interpreted as PowerShell commands.

