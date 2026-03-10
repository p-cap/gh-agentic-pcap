---
on:
  workflow_dispatch: {}
permissions:
  contents: read
  issues: read
  pull-requests: read
  security-events: read
tools:
  github:
    toolsets: [repos, issues, pull_requests, code_security]
network: defaults
safe-outputs:
  create-issue:
    max: 1
engine: gemini
---

# Dependabot alerts v2

## Objective
Create a manually triggered agentic workflow that retrieves all open Dependabot alerts for the current repository and generates a single aggregated issue. The workflow must recreate the issue each time it runs.

## Context
- The workflow runs only on manual invocation.
- It operates within the current repository.
- It uses GitHub App–based authentication through the built-in toolset permissions.
- A single consolidated issue provides visibility into all open alerts.

## Requirements
- Retrieve all Dependabot alerts for the current repository with `state = open`.
- Do not filter by severity; include all severities.
- Include for each alert:
  - Package name
  - Current (affected) version
  - Patched (fixed) version, if present
  - CVE identifier(s) when available; otherwise use the GHSA identifier
  - Severity
- Create one aggregated issue per workflow run.
- Close any previously created aggregated issue before posting the new one.
- Label the issue with:
  - `dependabot`
  - Severity labels for any severities present in this run:
    - `severity: critical`
    - `severity: high`
    - `severity: medium`
    - `severity: low`
- Use markdown formatting in the issue body with:
  - Header with repo name and timestamp
  - Severity summary
  - Table: Package | Current Version | Patched Version | CVE | Severity
- Add a hidden HTML marker in the issue body to assist in locating prior snapshots.
- Handle pagination and rate limiting.
- Use only the allowed GitHub toolsets: `repos`, `issues`, `pull_requests`, `code_security`.

## Steps
- Initialize
  - Obtain GitHub App token using the GitHub toolset.
  - Capture repository owner and name.
  - Capture execution timestamp (UTC).

- Retrieve alerts
  - Query the Dependabot alerts API via the `code_security` toolset.
  - Filter for open alerts.
  - Paginate until all alerts are retrieved.
  - Normalize all relevant alert fields.

- Aggregate results
  - Count alerts per severity.
  - Identify severity labels that apply.
  - Construct markdown body with:
    - Timestamped header
    - Severity summary
    - Table of alerts
    - Hidden marker: `<!-- dependabot-alerts-snapshot -->`

- Manage prior issue
  - Search for prior issues containing the hidden marker or matching the title prefix.
  - If one exists, close it with a comment referring to the new snapshot.

- Create new issue
  - Title: `Dependabot Alerts Snapshot – <repo> – <YYYY‑MM‑DD HH:MM UTC>`
  - Apply label `dependabot` and any severity labels.
  - Body: Assembled markdown content.
  - Output: issue number and issue URL via `safe-outputs.create-issue`.

## Constraints
- Manual trigger only; no scheduled or automatic execution.
- Operates within the current repository.
- Recreates the issue each run; does not update the prior issue.
- Uses GitHub toolsets only; no direct HTTP calls outside the provided capabilities.
- Avoid logging sensitive data.
- Must stay within safe-output limits defined in frontmatter.

## Pseudo-Code
```
TRIGGER: manual

alerts := FETCH_ALL_OPEN_DEPENDABOT_ALERTS(repo)

severity_counts := COUNT_BY_SEVERITY(alerts)

labels := ['dependabot']
FOR EACH severity IN ['critical','high','medium','low']
  IF severity_counts[severity] > 0 THEN
    labels.ADD('severity: ' + severity)
  END IF
END FOR

body := BUILD_MARKDOWN_WITH(
  header, severity_summary, table_rows, hidden_marker
)

prior_issue := FIND_EXISTING_SNAPSHOT_ISSUE()
IF prior_issue EXISTS THEN
  CLOSE(prior_issue)
END IF

new_issue := CREATE_ISSUE(title, labels, body)
OUTPUT(new_issue.number, new_issue.url)
```
``