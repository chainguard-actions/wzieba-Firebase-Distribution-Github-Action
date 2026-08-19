<!-- markdownlint-disable -->

# Hardening Report: wzieba--Firebase-Distribution-Github-Action/v1.5.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **wzieba--Firebase-Distribution-Github-Action/v1.5.1** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The workflow file references two actions with mutable refs instead of pinned 40-character commit SHAs:
- `uses: actions/checkout@master` (branch name — mutable)
- `uses: timheuer/base64-to-file@v1` (tag — mutable)
These can be silently updated by the upstream repository, enabling supply-chain attacks.

Locations:

- `.github/workflows/main.yml:7`
- `.github/workflows/main.yml:34`

### permissions (severity: medium)

missing-permissions: The workflow file has no top-level `permissions:` key and the single job (`run`) also has no job-level `permissions:` key. Without explicit permissions, the workflow inherits the repository default (often `write-all`), granting excessive access to the GITHUB_TOKEN.

Locations:

- `.github/workflows/main.yml:1`

### script-injection (severity: high)

Two `run:` steps directly interpolate GitHub Actions expressions inside shell command strings (rule a — direct expression interpolation), allowing an attacker to inject arbitrary shell commands:
1. `run: mv ${{ steps.fetch_credential_file.outputs.filePath }} /home/runner/work/...` — the step output is interpolated directly into the shell command without quoting or sanitization.
2. `if [[ "${{ steps.fail_check.outcome }}" == 'failure' ]]; then` — the step outcome expression is interpolated directly into the shell command. Any `${{ ... }}` inside a `run:` block is a script-injection risk because YAML template substitution occurs before the shell ever sees the value.

Locations:

- `.github/workflows/main.yml:37`
- `.github/workflows/main.yml:58`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, permissions, script-injection

**Notes:**

Fixed all three findings in .github/workflows/main.yml: (1) Pinned actions/checkout@master to full SHA 61b9e3751b92087fd0b06925ba6dd6314e06f089 and timheuer/base64-to-file@v1 to full SHA adaa40c0c581f276132199d4cf60afa07ce60eac with inline tag comments. (2) Added top-level `permissions: {}` to restrict GITHUB_TOKEN to no permissions. (3) Moved both ${{ steps.fetch_credential_file.outputs.filePath }} and ${{ steps.fail_check.outcome }} expressions out of run: shell strings into env: blocks (FILE_PATH and FAIL_CHECK_OUTCOME respectively), referencing them as plain shell variables in the scripts.

