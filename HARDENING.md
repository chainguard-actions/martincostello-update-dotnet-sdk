<!-- markdownlint-disable -->

# Hardening Report: martincostello--update-dotnet-sdk/v5.0.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **martincostello--update-dotnet-sdk/v5.0.2** was hardened automatically. 2 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### broad-permissions (severity: medium)

The workflow file ossf-scorecard.yml has a top-level `permissions: read-all` which grants overly broad read access to all scopes. This should be replaced with specific minimal permissions required by each job.

Locations:

- `.github/workflows/ossf-scorecard.yml:12`

### missing-permissions (severity: medium)

The reusable workflow update-dotnet-sdk.yml has no top-level `permissions:` key and its only job (`update-dotnet-sdk`) also has no job-level `permissions:` key. This means the workflow inherits whatever permissions the calling workflow provides, which could be overly broad. Explicit minimal permissions should be declared.

Locations:

- `.github/workflows/update-dotnet-sdk.yml:1`

## Iteration Notes

### Iteration 1

**Fixes applied:** broad-permissions, missing-permissions

**Notes:**

1. ossf-scorecard.yml: Replaced top-level `permissions: read-all` with `permissions: contents: read`. The job-level permissions (id-token: write, security-events: write) are preserved and override the top-level for the analysis job. contents:read is needed for the checkout step. 2. update-dotnet-sdk.yml: Added top-level `permissions: contents: read`. This reusable workflow uses externally-provided tokens (repo-token secret or GitHub App tokens) for all git push and PR creation operations, so the GITHUB_TOKEN only needs contents:read. Also removed the now-unnecessary zizmor ignore comment on the job.

