# code2docs-ai-core

## GitHub Action: Create New Repo

This repository contains a GitHub Action workflow that allows you to create a new repository in the current organization based on an input repository URL.

### Usage

To use this GitHub Action workflow, follow these steps:

1. Trigger the workflow with the following input parameters:
   - `repoUrl`: The GitHub repository URL in HTTPS format.
   - `branchName`: The branch name to use (default value is `main`).

2. The workflow will perform the following steps:
   - Print out the input parameters values in the workflow log.
   - Extract the `orgName` and `repoName` from the `repoUrl`, and concatenate them to form another parameter `docs_repo_name` = `orgName_repoName`.
   - Create a new repository in the current organization with the name `docs_repo_name`.

### Example

Here is an example of how to trigger the workflow:

```yaml
on:
  workflow_dispatch:
    inputs:
      repoUrl:
        description: 'The GitHub repository URL in HTTPS format'
        required: true
      branchName:
        description: 'The branch name to use'
        default: 'main'
```

This will trigger the workflow and create a new repository in the current organization based on the input `repoUrl`.
