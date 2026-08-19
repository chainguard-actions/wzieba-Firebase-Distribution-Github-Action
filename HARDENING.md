<!-- markdownlint-disable -->

# Hardening Report: wzieba--Firebase-Distribution-Github-Action/v1.7.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **wzieba--Firebase-Distribution-Github-Action/v1.7.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Multiple `run:` blocks in main.yml directly interpolate `${{ steps.*.outputs.* }}` and `${{ steps.*.outcome }}` expressions inside shell commands. These are workflow-controllable values that flow through YAML template substitution before the shell sees them, enabling script injection. Offending lines include:
- `run: mv ${{ steps.fetch_credential_file.outputs.filePath }} /home/runner/work/...` (line ~43)
- `echo "${{ steps.testing_outputs.outputs.FIREBASE_CONSOLE_URI }}"` and surrounding `if [[ -z "${{ steps.testing_outputs.outputs.* }}" ]]` checks (lines ~18–30)
- `if [[ "${{ steps.fail_check.outcome }}" == 'failure' ]]` (line ~76)

Locations:

- `.github/workflows/main.yml:18`
- `.github/workflows/main.yml:43`
- `.github/workflows/main.yml:76`

### unpinned-uses (severity: high)

Multiple `uses:` references are pinned to mutable branch names or version tags instead of immutable 40-character commit SHAs:
- `.github/workflows/main.yml`: `actions/checkout@master` (branch ref) and `timheuer/base64-to-file@v1` (version tag)
- `.github/workflows/publish_image.yml`: `actions/checkout@v3` (version tag)

Additionally, `action.yml` references a Docker image by a mutable tag rather than a SHA digest:
- `image: docker://ghcr.io/wzieba/firebase-distribution-github-action:sha-344aa66` — `sha-344aa66` is a tag, not a `@sha256:<digest>` reference.

Locations:

- `.github/workflows/main.yml:7`
- `.github/workflows/main.yml:51`
- `.github/workflows/publish_image.yml:22`
- `action.yml:30`

### missing-permissions (severity: medium)

`main.yml` has no top-level `permissions:` key and its only job (`run`) also has no job-level `permissions:` key. Without explicit permissions, the workflow inherits the default broad token permissions (typically `contents: write` for workflows triggered on push/pull_request), violating the principle of least privilege.

Locations:

- `.github/workflows/main.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all three findings:

1. script-injection: Moved all ${{ steps.*.outputs.* }} and ${{ steps.*.outcome }} expressions from run: shell strings into env: blocks in main.yml. Affected steps: 'Assert outputs of previous step are not empty' (FIREBASE_CONSOLE_URI, TESTING_URI, BINARY_DOWNLOAD_URI), 'Move credential file to repository location' (FILE_PATH), and 'Check if previous step failed as expected' (FAIL_CHECK_OUTCOME).

2. unpinned-uses: Pinned actions/checkout@master → @61b9e3751b92087fd0b06925ba6dd6314e06f089, timheuer/base64-to-file@v1 → @adaa40c0c581f276132199d4cf60afa07ce60eac in main.yml; actions/checkout@v3 → @a37ce9120846195fa4ece8f58b268e6043cb2f26 in publish_image.yml; Docker image in action.yml pinned with @sha256:943e4685e86f1066f58ebf97ed54181aef36b618b72fb38e82d5693944507ef8 (docker:// scheme and tag preserved).

3. missing-permissions: Added permissions: {} at the top level of main.yml to enforce least privilege.

