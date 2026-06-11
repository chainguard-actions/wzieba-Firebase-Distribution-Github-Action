<!-- markdownlint-disable -->

# Hardening Report: wzieba--Firebase-Distribution-Github-Action/v1.7.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `1`

Action **wzieba--Firebase-Distribution-Github-Action/v1.7.1** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

The `runs.image:` field in action.yml references the Docker image `ghcr.io/wzieba/firebase-distribution-github-action` using a mutable tag (`sha-344aa66`) rather than an immutable SHA256 digest (e.g. `@sha256:<64-hex-chars>`). Despite the tag name containing "sha-", it is still a regular mutable tag that can be silently changed by the registry owner, enabling a supply-chain attack. It should be pinned to a full SHA256 digest such as `ghcr.io/wzieba/firebase-distribution-github-action@sha256:<64-hex-digest>`.

Locations:

- `action.yml:44`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses

**Notes:**

Replaced the mutable Docker image tag `sha-344aa66` in action.yml line 44 with the immutable SHA256 digest. The image reference is now `docker://ghcr.io/wzieba/firebase-distribution-github-action@sha256:943e4685e86f1066f58ebf97ed54181aef36b618b72fb38e82d5693944507ef8 # sha-344aa66`, pinning it to an immutable digest while preserving the original tag as a comment.

