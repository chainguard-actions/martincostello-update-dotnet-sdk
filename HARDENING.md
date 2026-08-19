<!-- markdownlint-disable -->

# Hardening Report: martincostello--update-dotnet-sdk/v6.1.0

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **martincostello--update-dotnet-sdk/v6.1.0** was hardened automatically. 3 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### broad-permissions (severity: medium)

The workflow ossf-scorecard.yml has a top-level `permissions: read-all` which grants overly broad read access to all scopes. This should be replaced with specific minimal permissions for each job.

Locations:

- `.github/workflows/ossf-scorecard.yml:13`

### missing-permissions (severity: medium)

The workflow update-dotnet-sdk.yml has no top-level `permissions:` key and no job-level `permissions:` key on any of its jobs. Without explicit permissions, the workflow inherits the default (potentially broad) permissions of the calling workflow. A top-level `permissions: {}` or specific job-level permissions should be declared.

Locations:

- `.github/workflows/update-dotnet-sdk.yml:1`

### github-env-injection (severity: high)

In update-dotnet-sdk.yml, the 'Configure draft pull request' step writes the value of `inputs.draft` (sourced from the workflow input via the `DRAFT` env var) directly to `$GITHUB_OUTPUT` without sanitization. An attacker controlling the `draft` input could inject newlines to poison GITHUB_OUTPUT and set arbitrary output variables. The offending line is: `"draft=${draft}" >> ${env:GITHUB_OUTPUT}` where `$draft` is derived from `${env:DRAFT}` which is set to `${{ inputs.draft }}`. The required sanitization step (`printf '%s' ... | tr -d '\n\r'`) is absent.

Locations:

- `.github/workflows/update-dotnet-sdk.yml:285`

## Iteration Notes

### Iteration 1

**Fixes applied:** broad-permissions, missing-permissions, github-env-injection

**Notes:**

1. ossf-scorecard.yml: Replaced top-level `permissions: read-all` with specific `permissions: contents: read`. The job already has its own permissions block with `id-token: write` and `security-events: write` for the specific operations needed. 2. update-dotnet-sdk.yml: Added `permissions: {}` top-level block since the workflow uses explicit tokens (repo-token or GitHub App tokens) passed as secrets for all privileged operations, so the default GITHUB_TOKEN needs no permissions. 3. update-dotnet-sdk.yml 'Configure draft pull request' step: Added PowerShell sanitization (`-replace '[\r\n]', ''`) for both `$draft` and `$markReady` variables before writing them to `$GITHUB_OUTPUT`, preventing newline injection attacks via the `inputs.draft` workflow input.

