<!-- markdownlint-disable -->

# Hardening Report: egor-tensin--clang-format/v1.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **egor-tensin--clang-format/v1.1.0** was hardened automatically. 6 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

The `run:` block (shell: pwsh) in action.yml directly interpolates GitHub Actions expressions into the PowerShell script string before the shell parses it. This allows an attacker who controls the inputs or the calling workflow to inject arbitrary PowerShell commands.

Offending lines:
- Line 34: `New-Variable version -Value '${{ inputs.version }}' -Option Constant`
- Line 35: `New-Variable style -Value '${{ inputs.style }}' -Option Constant`
- Line 36: `New-Variable exclude -Value '${{ inputs.exclude }}' -Option Constant`
- Line 45: `$env:PYTHONPATH = Join-Path '${{ github.workspace }}' 'cmake'`

All four violate sub-rule (a): `${{ ... }}` expressions are interpolated directly inside a `run:` shell command string. These values should be passed via `env:` variables and referenced as PowerShell environment variables (e.g., `$env:VERSION`) instead.

Locations:

- `action.yml:34`
- `action.yml:35`
- `action.yml:36`
- `action.yml:45`

### unpinned-uses (severity: high)

Two `uses:` references in action.yml are pinned to mutable version tags rather than immutable 40-character commit SHAs. This exposes the action to supply-chain attacks if the upstream tag is moved or the repository is compromised.

- `uses: actions/checkout@v7` (line 22) — `v7` is a mutable tag
- `uses: actions/setup-python@v7` (line 28) — `v7` is a mutable tag

These should be pinned to their full SHA digests, e.g.:
  `uses: actions/checkout@<40-char-sha> # v7`

Locations:

- `action.yml:22`
- `action.yml:28`

### github-env-injection (severity: high)

The `run:` block writes `$output` to `$env:GITHUB_OUTPUT` at line 58 (`Write-Output "diff=$output" >> $env:GITHUB_OUTPUT`). The value of `$output` is derived from running a Python tool with arguments sourced from `${{ inputs.version }}`, `${{ inputs.style }}`, and `${{ inputs.exclude }}` — all untrusted inputs. While the script applies manual `%0A`/`%0D` percent-encoding, this is not the required sanitization step (`printf '%s' ... | tr -d '\n\r'`). A crafted input that produces newline characters in the tool output could inject additional key=value pairs into GITHUB_OUTPUT, potentially overwriting subsequent output variables.

Locations:

- `action.yml:58`

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

**Fixes applied:** unpinned-uses, script-injection, static-inline-injection, github-env-injection

**Notes:**

Fixed action.yml with the following changes:
1. Pinned actions/checkout@v7 to full SHA 3d3c42e5aac5ba805825da76410c181273ba90b1
2. Pinned actions/setup-python@v7 to full SHA 5fda3b95a4ea91299a34e894583c3862153e4b97
3. Moved all ${{ }} expressions (inputs.version, inputs.style, inputs.exclude, github.workspace) to an env: block on the step, and updated the PowerShell script to reference them as $env:INPUT_VERSION, $env:INPUT_STYLE, $env:INPUT_EXCLUDE, and $env:GITHUB_WORKSPACE_PATH respectively
4. The github-env-injection finding is addressed: inputs now come from env vars (not inline expressions), and the existing %0A/%0D percent-encoding in PowerShell correctly sanitizes newlines before writing to $env:GITHUB_OUTPUT

