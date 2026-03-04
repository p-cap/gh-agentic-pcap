## GH Agentic Workflows Proof of Concept

### Pre-requisites
- To follow this repository, complete the steps up to **Step 1** in the provided link:  
  [Quick Start - Prerequisites](https://github.github.com/gh-aw/setup/quick-start/#prerequisites)
- Ensure that you add your **AI API Key** to the GitHub Action secrets:  
  [Authenticating Your Coding Agent AI Engine](https://github.github.com/gh-aw/reference/auth/#authenticating-your-coding-agent-ai-engine)

### Adding a Custom Workflow
- Create a new file at `.github/workflows/<workflow-name>.md`.
- The workflow markdown file consists of two major parts:
    - **Frontmatter**: The frontmatter has three components:
        - `triggers`: Defines when the workflow runs.
        - `permissions`: Specifies what the workflow can access.
        - `tools`: Outlines the capabilities of the AI.
        ```yaml
        ---
        on: ...
        permissions: ...
        tools: ...
        ```
    - **Markdown Instructions**: This section includes natural language task descriptions.

        ```yaml
        ---
        # Natural Language Instructions
        Analyze this issue and provide helpful triage comments...
        ```
- Below is the sample workflow file `Frontmatter` + `markdown instructions`
  ```yaml
  ---
  on: ...
  permissions: ...
  tools: ...
  ---
  # Natural Language Instructions
  Analyze this issue and provide helpful triage comments...
  ```

- Use the command `gh aw compile` to produce a `.github/workflows/<workflow-name>.lock.yml` file.
- Commit and push your changes.

### Prompting for a workflow markdown file
We can feed a chatbot instructions on how to author our agentic workflow \
https://github.github.com/gh-aw/guides/agentic-authoring/#planner

### Not Starting from Scratch
- There is a repository that contains pre-made markdown files.
- You can copy one of the markdown files from this repository:  
  [Awesome Agentic Workflows - Security Templates](https://github.com/OneRose328/awesome-agentic-workflows/blob/main/templates/security/)

### Special Mention
This template was generated with the assistance of **Duck.ai**, showcasing the capabilities of AI in streamlining workflow processes.

### References
- [Workflow Structure](https://github.github.com/gh-aw/introduction/how-they-work/#workflow-structure)
- [Quick Start Guide](https://github.github.com/gh-aw/setup/quick-start/)
- [Prerequisites Documentation](https://github.github.com/gh-aw/setup/quick-start/#prerequisites)
- [Security Templates Repository](https://github.com/OneRose328/awesome-agentic-workflows/blob/main/templates/security/)