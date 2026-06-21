<!-- markdownlint-disable -->

# Hardening Report: egor-tensin--clang-format/v1.0.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **egor-tensin--clang-format/v1.0.2** was hardened automatically. 5 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

The `run:` block in action.yml (PowerShell/pwsh shell) directly interpolates GitHub Actions expressions inside the shell script string via YAML template substitution. Four expressions are interpolated: `${{ inputs.version }}` (line 24), `${{ inputs.style }}` (line 25), `${{ inputs.exclude }}` (line 26), and `${{ github.workspace }}` (line 43). These values are substituted into the PowerShell script text before the shell executes it, allowing an attacker who controls any of these inputs to inject arbitrary PowerShell commands. Sub-rule (a) violated: direct expression interpolation inside a `run:` block. Offending lines:
- `New-Variable version -Value '${{ inputs.version }}' -Option Constant`
- `New-Variable style -Value '${{ inputs.style }}' -Option Constant`
- `New-Variable exclude -Value '${{ inputs.exclude }}' -Option Constant`
- `$script_path = Join-Path '${{ github.workspace }}' .. $script_name`

Locations:

- `action.yml:24`
- `action.yml:25`
- `action.yml:26`
- `action.yml:43`

### github-env-injection (severity: high)

The `run:` block in action.yml writes to `$GITHUB_OUTPUT` (line 70: `Write-Output "diff=$output" >> $env:GITHUB_OUTPUT`) where `$output` is derived from running a Python script with attacker-controlled inputs (`${{ inputs.version }}`, `${{ inputs.style }}`, `${{ inputs.exclude }}`). These inputs are directly interpolated into the PowerShell script (lines 24–26), making `$output` tainted. The write to GITHUB_OUTPUT is not preceded by the required sanitization step (`printf '%s' ... | tr -d '\n\r'`). An attacker can inject newlines into the output to poison the GITHUB_OUTPUT file and set arbitrary environment variables or outputs for subsequent steps.

Locations:

- `action.yml:70`

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

**Fixes applied:** script-injection, github-env-injection, static-inline-injection

**Notes:**

Moved all ${{ }} expressions from the PowerShell run: block into the step's env: block: inputs.version → INPUT_VERSION, inputs.style → INPUT_STYLE, inputs.exclude → INPUT_EXCLUDE, github.workspace → GITHUB_WORKSPACE_PATH. The PowerShell script now references these as $env:INPUT_VERSION, $env:INPUT_STYLE, $env:INPUT_EXCLUDE, and $env:GITHUB_WORKSPACE_PATH respectively. The github-env-injection finding is also resolved: tainted inputs no longer flow directly into the script via template substitution, and the existing newline-replacement logic ($output.replace("`n", '%0A') etc.) already sanitizes the output before it is written to $GITHUB_OUTPUT.

