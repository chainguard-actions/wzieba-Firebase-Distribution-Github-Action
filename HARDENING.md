<!-- markdownlint-disable -->

# Hardening Report: wzieba--Firebase-Distribution-Github-Action/v1.6.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **wzieba--Firebase-Distribution-Github-Action/v1.6.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The `runs.image:` field in action.yml references a mutable Docker image tag (`sha-3379bab`) instead of an immutable SHA digest. Despite the tag name containing "sha-", it is just a label — not a cryptographic pin. A supply-chain attacker who can push to ghcr.io/wzieba/firebase-distribution-github-action could replace the image behind this tag. The reference should use a full SHA256 digest, e.g. `image: docker://ghcr.io/wzieba/firebase-distribution-github-action@sha256:<64-hex-char-digest>`.

Locations:

- `action.yml:37`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Replaced the mutable Docker image tag `ghcr.io/wzieba/firebase-distribution-github-action:sha-3379bab` with the immutable SHA256 digest `ghcr.io/wzieba/firebase-distribution-github-action@sha256:9aa8519f67390975e6d4d3a18e788df2e79354d1a25607f5f84cfe87a572a754` in action.yml. The original tag `sha-3379bab` is preserved as a comment for readability.

