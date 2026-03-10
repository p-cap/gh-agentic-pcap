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

# Dependabot alerts 

## Objective
Create a manual agentic workflow that retrieves Dependabot alerts for the current repository, prioritizes Critical and High severity items while avoiding breaking changes (Python focus), includes both direct and transitive dependencies, and outputs a single consolidated GitHub Issue containing the prioritized remediation plan.

## Context
- Repository scope: single repository.
- Trigger: manual dispatch only.
- Data source: Dependabot alerts API for this repository.
- Language/ecosystem focus: Python (e.g., pip/requirements.txt, Poetry, Pipenv).
- Implementation details are determined by the agent; this task specifies desired behavior, prioritization, and outputs.

## Requirements
- Use GitHub Dependabot alerts for the current repository only.
- Include both direct and transitive dependency alerts.
- Prioritize alerts by:
  1) Severity: Critical first, then High; Medium/Low can be included after if space allows.
  2) Lower blast radius first: prefer updates that affect fewer downstream dependents within the repo.
  3) Non-breaking updates first: prefer SemVer-compatible patch > minor > major (Python conventions).
- Breaking-change avoidance:
  - Prefer versions that satisfy existing version ranges when possible.
  - Defer major version bumps unless no compatible fix is available for high-severity alerts.
- Output: one consolidated Issue per run with a structured, skimmable format (see “Output Issue Format”).
- Do not open PRs; create an Issue only.
- Apply operational controls (see below).

## Steps
- Retrieve Dependabot alerts for the repository.
  - **Inputs:** repository identifier (implicit current repo), authentication with permissions to read Dependabot alerts.
  - **Outputs:** list of alerts with fields including severity, dependency name, current manifest/location, vulnerable range, patched versions, ecosystem, direct vs transitive, first detected, and advisory link.
  - **Constraints:** Dependabot alerts only; include direct and transitive.
- Normalize and enrich alert data.
  - **Inputs:** raw alert list.
  - **Process:** derive severity ranking; infer SemVer impact (patch/minor/major) for candidate fixed versions in Python ecosystem; estimate “downstream dependents affected” using available signals (e.g., whether dependency is direct vs transitive, how many top-level manifests reference it, presence of tight pins or compatibility constraints).
  - **Outputs:** enriched list with severity score, change type classification, estimated blast radius, and recommended target version (if compatible).
  - **Constraints:** do not require code execution; rely on available metadata and repository manifests readable by the agent.
- Prioritize and select items.
  - **Inputs:** enriched alerts.
  - **Process (pseudo-code):**
    ```
    FILTER alerts WHERE severity IN {Critical, High}
    ORDER BY severity DESC, blast_radius ASC, change_type (patch < minor < major), alert_age DESC
    LIMIT to max-items (see Operational Controls)
    ```
  - **Outputs:** prioritized subset with tie-break details recorded.
  - **Constraints:** if no compatible (non-major) fix exists for a Critical/High alert, include with a clear note that a major bump may be required.
- Build the consolidated issue content.
  - **Inputs:** prioritized subset; repository context.
  - **Outputs:** Markdown body following “Output Issue Format”, including sections for Summary, Prioritization rationale, Recommended actions, and a backlog appendix if applicable.
  - **Constraints:** safe-outputs applied; redact sensitive identifiers if required by policy.
- Create the GitHub Issue.
  - **Inputs:** issue title, body, labels, assignees (optional), milestone (optional).
  - **Outputs:** a single newly created Issue in the current repository.
  - **Constraints:** deduplication rules applied to avoid duplicates; if an open run-created issue exists with materially identical content, add a comment update instead of creating a new issue.

## Output Issue Format
- **Title (example):** `Security: Prioritized Dependabot Remediation Plan (Manual Run YYYY-MM-DD)`
- **Labels (suggested):** `security`, `dependabot`, `triage`, `automation`
- **Body sections (structure):**
  1. **Summary**
     - Total alerts reviewed; number selected (Critical/High).
     - Statement of prioritization policy (Critical/High; minimal blast radius; non-breaking-first).
  2. **Top Priorities (Actionable)**
     - For each item:
       - Dependency name, ecosystem, direct/transitive
       - Severity and advisory link
       - Affected range → recommended target version (non-breaking if available)
       - Change type classification: `PATCH | MINOR | MAJOR`
       - Estimated blast radius: `LOW | MEDIUM | HIGH` (brief rationale)
       - Proposed next step: “Prepare update to X → Y”, or “Evaluate major update (no compatible fix)”
  3. **Considerations & Risk**
     - Notes on pinned ranges, peer/conflict risk, CI focus areas.
  4. **Backlog (Optional)**
     - Medium/Low severities and/or de-prioritized items for visibility.
  5. **Audit Trail**
     - Run timestamp, filters applied, max-items limit, deduplication outcome.

## Constraints
- Do not write code or open PRs; only create a single consolidated Issue.
- Python-first semantics for version compatibility; treat patch/minor as non-breaking under SemVer assumptions.
- If multiple manifests (e.g., `requirements.txt`, `poetry.lock`) exist, report per-manifest context where available.
- Use Dependabot alerts only; do not mix with code scanning or secret scanning findings.

## Operational Controls
- **max-turns:** 1 (single-pass execution).
- **cache-memory:** enabled for the run to avoid redundant API calls within execution.
- **safe-outputs:** redact private repo details beyond what is necessary for triage; avoid exposing internal paths beyond manifest names.
- **max-items:** 20 (cap Top Priorities list; remaining go to Backlog).
- **deduplication:** if an open issue exists with the same title pattern from the last 7 days, add a comment update instead of creating a new issue.
- **rate-limits:** respect GitHub API pagination and backoff; fail gracefully with partial results if limits are encountered, documenting this in the Audit Trail.
- **idempotency:** include a run identifier and date in the issue title to distinguish runs when necessary.

## Acceptance Criteria
- Manual dispatch completes without errors.
- Dependabot alerts are fetched for the current repository, including both direct and transitive dependencies.
- Items are prioritized with Critical first, then High, ordering by minimal blast radius and non-breaking-first (patch/minor before major).
- A single consolidated Issue is created with the specified structure, labels, and rationale.
- Major-only fixes for Critical/High are included with a clear note that a breaking change may be required.
- Deduplication prevents duplicate issues within a 7-day window; comment updates are used instead.
- The Issue includes an Audit Trail with timestamp, filters, and max-items used.

## Notes
- This task relies on GitHub Dependabot alerts metadata and repository manifests to infer change-type and blast radius. The agent should avoid implementation-specific decisions and focus on prioritization and clear Issue output per this specification.
``