---
on:
  workflow_dispatch:
permissions:
  issues: read
tools:
  github:
    toolsets: [issues]
safe-outputs:
  create-issue:
    max: 1
network: defaults
engine: gemini
---
# Yo From Pcap

## Objective
Create an issue in the repository titled "Hello World from p-cap".

## Context
This workflow allows users to manually create an issue that describes a sample Agentic workflow.

## Requirements
- Title: "Hello World from p-cap"
- Description: "This is a sample Agentic workflow that creates an issue entitled Hello World from p-cap with a party emoji."

## Steps
- Trigger the workflow manually through GitHub.
- An issue should be created with the specified title and description.

## Constraints
- No additional constraints apply.
