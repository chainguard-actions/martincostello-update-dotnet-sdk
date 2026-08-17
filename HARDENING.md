<!-- markdownlint-disable -->

# Hardening Report: martincostello--update-dotnet-sdk/v6.1.3

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **martincostello--update-dotnet-sdk/v6.1.3** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### broad-permissions (severity: medium)

The workflow file ossf-scorecard.yml has a top-level `permissions: read-all` setting, which grants overly broad read access to all GitHub Actions scopes. This should be replaced with specific minimal permissions required by each job. Note: the file contains a `# zizmor: ignore[excessive-permissions]` comment acknowledging this, but it still violates the broad-permissions check.

Locations:

- `.github/workflows/ossf-scorecard.yml:13`

## Iteration Notes

### Iteration 1

**Fixes applied:** broad-permissions

**Notes:**

Replaced the top-level `permissions: read-all` in `.github/workflows/ossf-scorecard.yml` with `permissions: contents: read` (the minimum needed for the checkout step). Also added `contents: read` to the job-level permissions block alongside the existing `id-token: write` and `security-events: write`. The zizmor ignore comment was removed since the permissions are now properly scoped.

