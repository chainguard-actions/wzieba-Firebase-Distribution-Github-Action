<!-- markdownlint-disable -->

# Hardening Report: wzieba--Firebase-Distribution-Github-Action/v1.6.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **wzieba--Firebase-Distribution-Github-Action/v1.6.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Rule (a): Direct ${{ }} expression interpolation inside run: shell commands. In main.yml, two run: blocks embed expressions directly: (1) `run: mv ${{ steps.fetch_credential_file.outputs.filePath }} ...` interpolates a step output directly into the shell command string; (2) `if [[ "${{ steps.fail_check.outcome }}" == 'failure' ]]` interpolates a step output into a shell conditional. Any ${{ ... }} expression is substituted by the YAML templating engine before the shell sees the string, bypassing shell quoting and enabling injection.

Locations:

- `.github/workflows/main.yml:36`
- `.github/workflows/main.yml:63`

### unpinned-uses (severity: high)

Multiple unpinned action references found. In main.yml: `actions/checkout@master` (branch ref) and `timheuer/base64-to-file@v1` (version tag). In publish_image.yml: `actions/checkout@v3` (version tag). In action.yml: the Docker image `docker://ghcr.io/wzieba/firebase-distribution-github-action:sha-3379bab` uses a mutable short-SHA tag rather than an immutable SHA256 digest (e.g. @sha256:<64-hex-chars>). All of these can be silently replaced with malicious content without changing the reference.

Locations:

- `.github/workflows/main.yml:7`
- `.github/workflows/main.yml:34`
- `.github/workflows/publish_image.yml:22`
- `action.yml:28`

### missing-permissions (severity: medium)

The workflow file main.yml has no top-level `permissions:` key and its only job (`run`) also has no job-level `permissions:` key. Without explicit permissions, the GITHUB_TOKEN is granted default (potentially write) permissions, violating the principle of least privilege. The workflow is triggered by push, pull_request, and workflow_dispatch events, making this especially risky for pull_request triggers from forks.

Locations:

- `.github/workflows/main.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all three findings:

1. **script-injection** (main.yml): Moved both `${{ }}` expressions out of `run:` blocks into `env:` blocks. `steps.fetch_credential_file.outputs.filePath` is now referenced as `$FILE_PATH` and `steps.fail_check.outcome` is now referenced as `$FAIL_CHECK_OUTCOME` in their respective shell scripts.

2. **unpinned-uses**: Pinned all mutable action references to full commit SHAs: `actions/checkout@master` → `@61b9e3751b92087fd0b06925ba6dd6314e06f089`, `timheuer/base64-to-file@v1` → `@adaa40c0c581f276132199d4cf60afa07ce60eac`, `actions/checkout@v3` (publish_image.yml) → `@f43a0e5ff2bd294095638e18286ca9a3d1956744`. Pinned the Docker image in action.yml with its immutable sha256 digest while preserving the `docker://` scheme and `:sha-3379bab` tag.

3. **missing-permissions** (main.yml): Added `permissions: contents: read` at the top-level workflow scope to enforce least-privilege for the GITHUB_TOKEN.

