---
on: workflow_dispatch
permissions:
  issues: read
tools:
  github:
    toolsets: [issues]
---
# Hello World P-cap

## Objective
This workflow aims to create a GitHub issue with the title "Hello World from p-cap!" accompanied by a celebration emoji, and print a console message with a salute emoji.

## Context
This workflow will be triggered manually via the GitHub interface. It will create an issue and provide console feedback about the action taken.

## Requirements
- Create an issue titled "Hello World from p-cap!" with the celebration emoji 🎉.
- Print "Hello World from p-cap!" in the console with a salute emoji 🫡.
- Include a link back to the created issue in the console output.

## Steps
- Use the `github` toolset to create the issue with the specified title.
- Capture the issue link generated from the creation process.
- Print the message "Hello World from p-cap!" along with the salute emoji and include a link to the created issue.

## Constraints
- The workflow must have no permissions beyond reading issues.
- Ensure proper handling of any potential errors during issue creation.
