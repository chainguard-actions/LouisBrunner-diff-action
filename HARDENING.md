<!-- markdownlint-disable -->

# Hardening Report: LouisBrunner--diff-action/v1.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **LouisBrunner--diff-action/v1.0.0** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### unpinned-uses (severity: high)

Both workflow files use `actions/checkout@v1`, which is a mutable tag reference rather than a pinned 40-character commit SHA. This means the action could be silently updated to a different (potentially malicious) version without any change to the workflow file. All `uses:` references should be pinned to a full SHA (e.g., `actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v1`). In examples.yml, `actions/checkout@v1` appears in all 21 jobs.

Locations:

- `.github/workflows/build.yml:14`
- `.github/workflows/examples.yml:10`

### missing-permissions (severity: medium)

Neither `.github/workflows/build.yml` nor `.github/workflows/examples.yml` declares a top-level `permissions:` key, and no individual job within either file declares its own `permissions:` block. Without explicit permissions, the GITHUB_TOKEN is granted its default (broad) permissions, violating the principle of least privilege. A `permissions:` block with only the required scopes (e.g., `contents: read`) should be added at the top level or per-job.

Locations:

- `.github/workflows/build.yml:1`
- `.github/workflows/examples.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** unpinned-uses, missing-permissions

**Notes:**

Fixed both workflow files: (1) Pinned all 22 occurrences of `actions/checkout@v1` to the full SHA `50fbc622fc4ef5163becd7fab6573eac35f8462e` with `# v1` comment for readability. (2) Added top-level `permissions: contents: read` to both build.yml and examples.yml. For the two notification jobs in examples.yml (`test_output_notifs_good` and `test_output_notifs_bad`) that use GITHUB_TOKEN with `notify_issue: true` and `notify_check: true`, per-job permissions blocks were added with `checks: write` and `issues: write` to support those operations while keeping least privilege.

