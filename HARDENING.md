<!-- markdownlint-disable -->

# Hardening Report: LouisBrunner--diff-action/v3.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **LouisBrunner--diff-action/v3.0.0** was hardened automatically. 4 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): The `run:` block in the `retag` job directly interpolates `${{ github.ref_name }}` into a shell command string. A tag name is attacker-influenced and can contain shell metacharacters, enabling command injection. Offending line: `TRUNC_VER=$(echo ${{ github.ref_name }} | cut -d '.' -f 1)`

Locations:

- `.github/workflows/update-tags.yaml:14`

### github-env-injection (severity: high)

The `run:` block writes `TRUNC_VER` — a value derived from the untrusted `${{ github.ref_name }}` expression — to `$GITHUB_OUTPUT` without first sanitizing it with `printf '%s' ... | tr -d '\n\r'`. A crafted tag name containing newlines could inject arbitrary environment variables or output entries. Offending line: `echo "tag=$TRUNC_VER" >> $GITHUB_OUTPUT`

Locations:

- `.github/workflows/update-tags.yaml:16`

### unpinned-uses (severity: high)

Multiple workflow files reference external actions using mutable version tags instead of pinned 40-character commit SHAs, making them vulnerable to supply-chain attacks if the tag is moved. Failing references: build.yml — `actions/checkout@v6`, `oven-sh/setup-bun@v2`; examples.yml — `actions/checkout@v6` (used in every job); lint-gha.yml — `actions/checkout@v6`, `ibiqlik/action-yamllint@v3`; update-tags.yaml — `actions/checkout@v6`, `rickstaa/action-create-tag@v1`.

Locations:

- `.github/workflows/build.yml:11`
- `.github/workflows/build.yml:13`
- `.github/workflows/examples.yml:13`
- `.github/workflows/lint-gha.yml:18`
- `.github/workflows/lint-gha.yml:19`
- `.github/workflows/update-tags.yaml:9`
- `.github/workflows/update-tags.yaml:20`

### missing-permissions (severity: medium)

The following workflow files have no top-level `permissions:` block and no job-level `permissions:` block on any of their jobs. Without explicit permissions, the GITHUB_TOKEN is granted its default (potentially broad) permissions. `build.yml` (job: build), `examples.yml` (all jobs), `lint-gha.yml` (job: lint), and `update-tags.yaml` (job: retag — the `permissions:` key present is nested under a `uses:` step, which is not a valid job-level permissions declaration).

Locations:

- `.github/workflows/build.yml:1`
- `.github/workflows/examples.yml:1`
- `.github/workflows/lint-gha.yml:1`
- `.github/workflows/update-tags.yaml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, github-env-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all four findings across four workflow files:

1. script-injection (update-tags.yaml): Moved `${{ github.ref_name }}` into an `env:` block as `REF_NAME` and referenced it as `"$REF_NAME"` in the shell script.

2. github-env-injection (update-tags.yaml): Sanitized the derived value with `printf '%s' ... | tr -d '\n\r'` before writing to `$GITHUB_OUTPUT`.

3. unpinned-uses: Pinned all four actions to full 40-character commit SHAs — actions/checkout@v6 → d23441a48e516b6c34aea4fa41551a30e30af803, oven-sh/setup-bun@v2 → 0c5077e51419868618aeaa5fe8019c62421857d6, ibiqlik/action-yamllint@v3 → 2576378a8e339169678f9939646ee3ee325e845c, rickstaa/action-create-tag@v1 → a1c7777fcb2fee4f19b0f283ba888afa11678b72.

4. missing-permissions: Added top-level `permissions: {}` and appropriate job-level permissions to all four files. For update-tags.yaml, moved the misplaced step-level `permissions:` block to the correct job level with `contents: write`. For examples.yml jobs that use notify_issue/notify_check, added `issues: write` and `checks: write` in addition to `contents: read`.

