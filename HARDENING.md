<!-- markdownlint-disable -->

# Hardening Report: peter-evans--close-fork-pulls/v3.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **peter-evans--close-fork-pulls/v3.0.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### script-injection (severity: high)

Sub-rule (a): Direct expression interpolation of workflow_dispatch inputs inside run: shell commands. In 'Tag new target', `${{ github.event.inputs.main_version }}` and `${{ github.event.inputs.target }}` are interpolated directly into the shell command `git tag -f ${{ github.event.inputs.main_version }} ${{ github.event.inputs.target }}`. In 'Push new tag', `${{ github.event.inputs.main_version }}` is interpolated into `git push origin ${{ github.event.inputs.main_version }} --force`. An attacker with workflow_dispatch access could inject arbitrary shell commands via these inputs.

Locations:

- `.github/workflows/update-major-version.yml:29`
- `.github/workflows/update-major-version.yml:31`

### unpinned-uses (severity: high)

All uses: references across all workflow files use mutable version tags instead of pinned 40-character SHA digests, making the action vulnerable to supply-chain attacks if the referenced tag is moved. Failing references include: peter-evans/enable-pull-request-automerge@v3, actions/checkout@v4, actions/setup-node@v4, actions/upload-artifact@v4 (multiple), actions/download-artifact@v4 (multiple), peter-evans/create-pull-request@v5 (multiple), peter-evans/slash-command-dispatch@v3.

Locations:

- `.github/workflows/automerge-dependabot.yml:9`
- `.github/workflows/ci.yml:19`
- `.github/workflows/ci.yml:20`
- `.github/workflows/ci.yml:30`
- `.github/workflows/ci.yml:33`
- `.github/workflows/ci.yml:41`
- `.github/workflows/ci.yml:45`
- `.github/workflows/ci.yml:50`
- `.github/workflows/slash-command-dispatch.yml:8`
- `.github/workflows/update-major-version.yml:20`

### missing-permissions (severity: medium)

Three workflow files have no top-level permissions: key and no job-level permissions: keys on any of their jobs. Without explicit permissions, workflows inherit the repository's default token permissions, which may be overly broad. Affected files: automerge-dependabot.yml, slash-command-dispatch.yml, and update-major-version.yml.

Locations:

- `.github/workflows/automerge-dependabot.yml:1`
- `.github/workflows/slash-command-dispatch.yml:1`
- `.github/workflows/update-major-version.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** script-injection, unpinned-uses, missing-permissions

**Notes:**

Fixed all three findings across four workflow files: (1) script-injection in update-major-version.yml — moved github.event.inputs expressions into env: blocks and referenced them as plain shell variables; (2) unpinned-uses — pinned all 10 action references across automerge-dependabot.yml, ci.yml, slash-command-dispatch.yml, and update-major-version.yml to full 40-char SHAs with tag comments; (3) missing-permissions — added top-level permissions blocks to automerge-dependabot.yml (pull-requests: write), slash-command-dispatch.yml (issues: read, pull-requests: read), and update-major-version.yml (contents: write).

