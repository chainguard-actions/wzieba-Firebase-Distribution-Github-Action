<!-- markdownlint-disable -->

# Hardening Report: wzieba--Firebase-Distribution-Github-Action/v1.5.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **wzieba--Firebase-Distribution-Github-Action/v1.5.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Two `uses:` references in .github/workflows/main.yml are pinned to mutable refs instead of immutable 40-character commit SHAs:
1. `uses: actions/checkout@master` — pinned to a branch name (`master`), which can change at any time.
2. `uses: timheuer/base64-to-file@v1` — pinned to a tag (`v1`), which can be moved to a different commit.
Both should be replaced with full SHA pins (e.g. `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4`).

Locations:

- `.github/workflows/main.yml:8`
- `.github/workflows/main.yml:35`

### script-injection (severity: high)

Sub-rule (a): A `${{ ... }}` expression is interpolated directly inside a `run:` shell command string. The step 'Move credential file to repository location' uses `${{ steps.fetch_credential_file.outputs.filePath }}` directly in the shell command:

  `run: mv ${{ steps.fetch_credential_file.outputs.filePath }} /home/runner/work/...`

The expression is substituted into the shell command before the shell parses it, allowing a malicious `filePath` value to inject arbitrary shell commands. The value should be passed via an `env:` variable and then double-quoted in the script.

Locations:

- `.github/workflows/main.yml:38`

### missing-permissions (severity: medium)

The workflow file .github/workflows/main.yml has no top-level `permissions:` key, and the single job `run` also has no `permissions:` key. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad (e.g. write access to contents). A minimal `permissions:` block should be added at the top level or per-job level.

Locations:

- `.github/workflows/main.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, missing-permissions

**Notes:**

Fixed all three findings in .github/workflows/main.yml:
1. unpinned-uses: Pinned `actions/checkout@master` to SHA `61b9e3751b92087fd0b06925ba6dd6314e06f089 # master` and `timheuer/base64-to-file@v1` to SHA `adaa40c0c581f276132199d4cf60afa07ce60eac # v1`.
2. script-injection: Moved `${{ steps.fetch_credential_file.outputs.filePath }}` into an `env:` block as `FILE_PATH` and referenced it as `"$FILE_PATH"` in the shell command.
3. missing-permissions: Added `permissions: {}` at the top level to enforce least-privilege token access.

