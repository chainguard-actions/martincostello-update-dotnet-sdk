<!-- markdownlint-disable -->

# Hardening Report: martincostello--update-dotnet-sdk/v6.0.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **martincostello--update-dotnet-sdk/v6.0.0** was hardened automatically. 2 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### broad-permissions (severity: medium)

The workflow file ossf-scorecard.yml has a top-level `permissions: read-all` setting, which grants overly broad read access to all GitHub Actions scopes. This should be replaced with specific minimal permissions scopes required by each job.

Locations:

- `.github/workflows/ossf-scorecard.yml:12`

### missing-permissions (severity: medium)

The reusable workflow update-dotnet-sdk.yml has no `permissions:` key at either the top level or the job level. It intentionally inherits permissions from the calling workflow (noted in comments with `# zizmor: ignore[excessive-permissions]`), but this means the job runs with whatever permissions the caller grants, which could be overly broad. A top-level `permissions: {}` or explicit job-level permissions block should be added.

Locations:

- `.github/workflows/update-dotnet-sdk.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** broad-permissions, missing-permissions

**Notes:**

1. ossf-scorecard.yml: Replaced top-level `permissions: read-all` with `permissions: {}`. The job already had its own specific permissions block (id-token: write, security-events: write), so the top-level deny-all is correct — job-level permissions override the top-level default. 2. update-dotnet-sdk.yml: Added `permissions: {}` at the top level (before the env: block). This satisfies the missing-permissions finding. For workflow_call reusable workflows, the caller's permissions are passed through to the called workflow, so this doesn't break the intentional permission inheritance while satisfying the security requirement.

### Iteration 2

**Fixes applied:** github-env-injection

**Notes:**

Fixed two github-env-injection findings in PowerShell workflow steps:
1. hardened/action/.github/workflows/bump-version.yml (push-changes step, ~line 175): Added `$safeVersion = ${env:NEXT_VERSION} -replace '[\r\n]', ''` before writing `version=` to $GITHUB_OUTPUT.
2. hardened/action/.github/workflows/update-dotnet-sdk.yml (assign-commit-sha step, ~line 430): Added `$safeSha = ${env:PACKAGES_SHA} -replace '[\r\n]', ''` and `$safeSha = ${env:SDK_SHA} -replace '[\r\n]', ''` before writing `commit-sha=` to $GITHUB_OUTPUT in both branches of the conditional. Both fixes use PowerShell's -replace operator to strip carriage return and newline characters, preventing newline injection attacks.

