<!-- markdownlint-disable -->

# Hardening Report: wzieba--Firebase-Distribution-Github-Action/v1.7.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **wzieba--Firebase-Distribution-Github-Action/v1.7.0** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The action.yml uses a Docker image referenced by a mutable tag rather than an immutable SHA digest. The image `docker://ghcr.io/wzieba/firebase-distribution-github-action:sha-344aa66` uses a tag (`sha-344aa66`) — despite the tag name containing "sha-", this is NOT a cryptographic digest. A supply-chain attacker could push a malicious image to this tag. The image reference must use a SHA256 digest in the form `ghcr.io/wzieba/firebase-distribution-github-action@sha256:<64-hex-chars>` to be pinned immutably.

Locations:

- `action.yml:38`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Replaced the mutable Docker image tag `ghcr.io/wzieba/firebase-distribution-github-action:sha-344aa66` with the immutable SHA256 digest `ghcr.io/wzieba/firebase-distribution-github-action@sha256:943e4685e86f1066f58ebf97ed54181aef36b618b72fb38e82d5693944507ef8` in action.yml line 38. The original tag is preserved as a comment for readability.

