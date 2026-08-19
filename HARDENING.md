<!-- markdownlint-disable -->

# Hardening Report: martincostello--update-dotnet-sdk/v6.1.1

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **martincostello--update-dotnet-sdk/v6.1.1** was hardened automatically. 3 finding(s) were identified and resolved across 2 iteration(s).

## Findings Fixed

### broad-permissions (severity: medium)

The workflow file ossf-scorecard.yml has a top-level `permissions: read-all` key, which grants overly broad read access to all scopes. This should be replaced with specific minimal permissions at the job level only.

Locations:

- `.github/workflows/ossf-scorecard.yml:11`

### missing-permissions (severity: medium)

The reusable workflow update-dotnet-sdk.yml has no top-level `permissions:` key and the `update-dotnet-sdk` job has no job-level `permissions:` key. Without an explicit permissions block, the workflow inherits whatever permissions the calling workflow grants, which may be overly broad.

Locations:

- `.github/workflows/update-dotnet-sdk.yml:1`

### github-env-injection (severity: high)

In update-dotnet-sdk.yml, the `Configure draft pull request` step maps the caller-controlled input `inputs.draft` into the env var `DRAFT` and then writes it directly to `$GITHUB_OUTPUT` via `"draft=${draft}" >> ${env:GITHUB_OUTPUT}` without applying the required sanitization (`printf '%s' ... | tr -d '\n\r'`). A caller can inject newlines into the GITHUB_OUTPUT file to set arbitrary output variables.

Locations:

- `.github/workflows/update-dotnet-sdk.yml:290`

## Iteration Notes

### Iteration 1

**Fixes applied:** broad-permissions, missing-permissions, github-env-injection

**Notes:**

1. ossf-scorecard.yml: Replaced top-level `permissions: read-all` with `permissions: {}` — the job already has specific minimal permissions (id-token: write, security-events: write) at the job level.
2. update-dotnet-sdk.yml: Added top-level `permissions: contents: write` and `pull-requests: write` block — the minimal permissions needed for the reusable workflow to push commits and manage PRs.
3. update-dotnet-sdk.yml (Configure draft pull request step): Sanitized `draft` and `mark-ready` values using PowerShell's `-replace '[\r\n]', ''` before writing to `$GITHUB_OUTPUT` to prevent newline injection attacks.

### Iteration 2

**Fixes applied:** github-env-injection

**Notes:**

Fixed all four github-env-injection findings by adding newline sanitization (PowerShell -replace '[\r\n]', '') before writing untrusted step output values to $GITHUB_OUTPUT:
1. update-dotnet-sdk.yml assign-token step: sanitized $accessToken into $safeAccessToken before writing 'access-token=...' to GITHUB_OUTPUT.
2. update-dotnet-sdk.yml 'Assign Git commit SHA' step: sanitized PACKAGES_SHA and SDK_SHA into $safeCommitSha before writing 'commit-sha=...' to GITHUB_OUTPUT.
3. bump-sha.yml 'Bump Git SHA' step: sanitized $sha into $safeSha before writing 'sha=...' to GITHUB_OUTPUT.
4. bump-version.yml 'Push changes to GitHub' step: sanitized $env:NEXT_VERSION into $safeNextVersion before writing 'version=...' to GITHUB_OUTPUT.

