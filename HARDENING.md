<!-- markdownlint-disable -->

# Hardening Report: reviewdog--action-setup/v1.5.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **reviewdog--action-setup/v1.5.0** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Two steps in .github/workflows/reviewdog.yml use `reviewdog/action-suggester@master` — a mutable branch ref rather than a pinned 40-character commit SHA. This means the action code can change at any time without notice, enabling a supply-chain attack. Both occurrences should be replaced with a full SHA pin (e.g. `reviewdog/action-suggester@<40-hex-sha> # vX.Y.Z`).

Locations:

- `.github/workflows/reviewdog.yml:35`
- `.github/workflows/reviewdog.yml:46`

### missing-permissions (severity: medium)

.github/workflows/release.yml has no top-level `permissions:` key and none of its jobs define a `permissions:` block. Without explicit permissions, the workflow inherits the repository's default token permissions (which may be write-all), violating the principle of least privilege.

Locations:

- `.github/workflows/release.yml:1`

### missing-permissions (severity: medium)

.github/workflows/reviewdog.yml has no top-level `permissions:` key and none of its jobs define a `permissions:` block. Without explicit permissions, the workflow inherits the repository's default token permissions (which may be write-all), violating the principle of least privilege.

Locations:

- `.github/workflows/reviewdog.yml:1`

### missing-permissions (severity: medium)

.github/workflows/test.yml has no top-level `permissions:` key and none of its jobs define a `permissions:` block. Without explicit permissions, the workflow inherits the repository's default token permissions (which may be write-all), violating the principle of least privilege.

Locations:

- `.github/workflows/test.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all 4 findings: (1) Pinned both `reviewdog/action-suggester@master` references in .github/workflows/reviewdog.yml to commit SHA `b637bf826ab31f4d294e228138c8778f0a60b9c2 # master`. (2) Added `permissions: {}` to reviewdog.yml (jobs pass secrets directly to actions, no GITHUB_TOKEN write access needed). (3) Added `permissions: contents: write` and `pull-requests: write` to release.yml (release job creates GitHub releases; release-check job posts PR comments). (4) Added `permissions: contents: read` to test.yml (only checks out code and runs the local action).

