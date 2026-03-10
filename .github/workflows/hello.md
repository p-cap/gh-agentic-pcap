--- 
name: "Manual Issue Creator"
description: "Creates an issue titled \"Hello from p-cap!\" when triggered manually."
on:
  workflow_dispatch: {}
permissions:
  contents: read
safe-outputs:
  create-issue:
    max: 1
# Optional: keep tools minimal; the agent can still emit safe-outputs.
# tools:
#   github:
#     toolsets: [issues]
---
# create a github agentic workflow that: creates an issue titled "Hello from p-cap!" when triggered manually

## Objective
Create a workflow that generates a new GitHub issue with a fixed title upon manual execution.

## Context
The repository currently has no automated workflow for creating issues. The workflow should only run when manually triggered and must open a new issue with the specified title.

## Requirements
- The workflow must be triggered manually by a user.
- The workflow must create a new issue in the current repository.
- The issue title must be exactly: **Hello from p-cap!**
- The workflow must follow GitHub Agentic Workflows conventions, including valid frontmatter and markdown formatting.

## Steps
- Detect the manual trigger event.
- Validate that the repository supports issue creation.
- Prepare an issue creation request containing the required title.
- Submit a request to create the issue.
- Confirm that the issue was created successfully.

## Constraints
- Do not include implementation-specific code.
- Do not reference specific API endpoints.
- Use neutral technical language and allow the coding agent to determine implementation details.
