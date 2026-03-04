---
name: "Hello World"
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
# create a github agentic workflow that: prints hello world to console and creates a github issue with celebration emoji

## Objective
Create a simple demonstration workflow that outputs a greeting message to the console and creates a corresponding GitHub issue to show basic agentic workflow capabilities.

## Context
This is a hello world example workflow for learning and demonstration purposes. The workflow should interact with both the local console environment and the GitHub API to create an issue.

## Requirements
- Output the string "Hello World" to the console/stdout
- Create a GitHub issue in the current repository with the title "🎉 Hello World from p-cap"
- Use the celebration emoji (🎉) in the issue title
- Complete both actions successfully

## Steps
- Print "Hello World" to the console
- Create a GitHub issue with the title "🎉 Hello World from p-cap" in the current repository
- Verify both actions completed successfully

## Constraints
- Use the GitHub API or available GitHub CLI tools to create the issue
- The workflow should execute within a GitHub Actions environment
- Assume necessary GitHub credentials/permissions are available
