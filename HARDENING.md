<!-- markdownlint-disable -->

# Hardening Report: martincostello--update-dotnet-sdk/v6.1.2

> This file was generated automatically by the hardening agent.

**Policy SHA:** `d636be7e43ef829af6e853da6b3c7566db9f72fe`

**Test Policy SHA:** `843adf9e4b8f85d0c08b27b9d0b09dd094b54702`

**Harden Agent Version:** `2`

Action **martincostello--update-dotnet-sdk/v6.1.2** was hardened automatically. 1 finding(s) were identified and resolved across 1 iteration(s).

## Findings Fixed

### broad-permissions (severity: medium)

The workflow file ossf-scorecard.yml has a top-level `permissions: read-all` setting, which grants overly broad read access to all GitHub Actions scopes. This should be replaced with specific minimal permissions. (Note: the file contains a zizmor suppression comment, but the broad-permissions check still applies per the security rules.)

Locations:

- `.github/workflows/ossf-scorecard.yml:11`

## Iteration Notes

### Iteration 1

**Fixes applied:** broad-permissions

**Notes:**

Replaced the top-level `permissions: read-all` in `.github/workflows/ossf-scorecard.yml` with specific minimal permissions `contents: read`. The job already had properly scoped job-level permissions (`id-token: write` and `security-events: write`), so only `contents: read` is needed at the workflow level for the checkout step. The zizmor suppression comment was removed since the issue is now properly resolved.

