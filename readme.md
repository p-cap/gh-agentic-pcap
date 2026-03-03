## GH Agentic Workflows Proof of Concept
### Pre-requisites
 - If you want to follow this repo, follow the steps until Step 1 of the provided link:
 https://github.github.com/gh-aw/setup/quick-start/#prerequisites 
- Make sure to add AI API Key to Github Action secrets:
https://github.github.com/gh-aw/reference/auth/#authenticating-your-coding-agent-ai-engine

### Adding a custom workflow
- Add `.github/workflows/<workflow-name>.md`
- Copy one of the md files from repo:
https://github.com/OneRose328/awesome-agentic-workflows/blob/main/templates/security/
- `gh aw compile` -> this command produces a `.github/workflows/<workflow-name>.lock.yml` file
- commit and push changes

### REFERENCE
- https://github.github.com/gh-aw/setup/quick-start/
- https://github.github.com/gh-aw/setup/quick-start/#prerequisites
- https://github.com/OneRose328/awesome-agentic-workflows/blob/main/templates/security/