<!-- markdownlint-disable -->

# Hardening Report: LouisBrunner--diff-action/v2.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **LouisBrunner--diff-action/v2.1.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

All workflow files use mutable tag-based refs (e.g. @v4, @v3) instead of pinned 40-character SHA commits. This exposes the workflow to supply-chain attacks if the referenced tag is moved or the upstream repository is compromised. Failing references: build.yml — actions/checkout@v4, actions/setup-node@v4; examples.yml — actions/checkout@v4 (repeated across all jobs); lint-gha.yml — actions/checkout@v4, ibiqlik/action-yamllint@v3.

Locations:

- `.github/workflows/build.yml:12`
- `.github/workflows/build.yml:14`
- `.github/workflows/examples.yml:13`
- `.github/workflows/lint-gha.yml:16`
- `.github/workflows/lint-gha.yml:17`

### missing-permissions (severity: medium)

None of the workflow files define a top-level `permissions:` key, and no individual job defines its own `permissions:` block. Without explicit permissions, workflows run with the default (potentially broad) token permissions. All three workflow files are affected: build.yml, examples.yml, and lint-gha.yml.

Locations:

- `.github/workflows/build.yml:1`
- `.github/workflows/examples.yml:1`
- `.github/workflows/lint-gha.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed all three workflow files: (1) Pinned all action refs to full 40-char SHAs — actions/checkout@v4 → @11d5960a326750d5838078e36cf38b85af677262, actions/setup-node@v4 → @49933ea5288caeca8642d1e84afbd3f7d6820020, ibiqlik/action-yamllint@v3 → @2576378a8e339169678f9939646ee3ee325e845c — preserving the original tag in a trailing comment. (2) Added top-level `permissions: {}` to all three files to deny all permissions by default, and added per-job `permissions:` blocks granting only `contents: read` for standard jobs, plus `issues: write`, `checks: write`, and `pull-requests: write` for the notification-related jobs in examples.yml that use GITHUB_TOKEN with notify_issue/notify_check.

