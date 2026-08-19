<!-- markdownlint-disable -->

# Hardening Report: wzieba--Firebase-Distribution-Github-Action/v1.7.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **wzieba--Firebase-Distribution-Github-Action/v1.7.1** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

action.yml uses a Docker image with a mutable short-git-SHA tag (`sha-344aa66`) instead of an immutable SHA-256 digest. This means the image could be silently replaced with a different version. The reference `docker://ghcr.io/wzieba/firebase-distribution-github-action:sha-344aa66` must be replaced with a `sha256:<64-hex-char-digest>` form.

Additionally, .github/workflows/main.yml references `actions/checkout@master` (mutable branch) and `timheuer/base64-to-file@v1` (mutable tag), and .github/workflows/publish_image.yml references `actions/checkout@v3` (mutable tag). All must be pinned to full 40-character commit SHAs.

Locations:

- `action.yml:36`
- `.github/workflows/main.yml:8`
- `.github/workflows/main.yml:57`
- `.github/workflows/publish_image.yml:28`

### script-injection (severity: high)

Multiple `run:` blocks in .github/workflows/main.yml directly interpolate `${{ ... }}` expressions into shell commands (sub-rule a), allowing an attacker to inject arbitrary shell code if any of those values are attacker-controlled.

1. Line 20: `echo "${{ steps.testing_outputs.outputs.FIREBASE_CONSOLE_URI }}"` and similar expressions are interpolated directly into the shell script in the 'Assert outputs' step.
2. Line 62: `run: mv ${{ steps.fetch_credential_file.outputs.filePath }} /home/runner/work/...` — the step output is interpolated directly into a shell `mv` command without quoting or env-var indirection.
3. Line 73: `if [[ "${{ steps.fail_check.outcome }}" == 'failure' ]]` — step outcome is interpolated directly into the shell.

All `${{ ... }}` expressions must be moved to `env:` variables and then referenced as quoted shell variables (e.g., `"$VAR"`).

Locations:

- `.github/workflows/main.yml:20`
- `.github/workflows/main.yml:62`
- `.github/workflows/main.yml:73`

### missing-permissions (severity: medium)

.github/workflows/main.yml has no top-level `permissions:` block and its single job (`run`) also has no `permissions:` block. Without explicit permissions, the workflow inherits the repository's default token permissions, which may be overly broad (e.g., write access to contents). A minimal `permissions:` block should be added.

Locations:

- `.github/workflows/main.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, script-injection, missing-permissions

**Notes:**

Fixed all three findings:
1. unpinned-uses: Pinned Docker image in action.yml with SHA256 digest (preserving docker:// scheme and tag inline). Pinned actions/checkout@master and timheuer/base64-to-file@v1 in main.yml, and actions/checkout@v3 in publish_image.yml to full 40-char commit SHAs.
2. script-injection: Moved all ${{ }} expressions in main.yml run: blocks to env: variables and referenced them as quoted shell variables ($VAR_NAME) in the shell scripts — fixed in the 'Assert outputs', 'Move credential file', and 'Check if previous step failed' steps.
3. missing-permissions: Added top-level 'permissions: contents: read' block to main.yml.

