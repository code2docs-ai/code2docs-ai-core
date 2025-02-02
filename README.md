# code2docs-ai-core

## GitHub Action: Code2Docs-ai Agent Workflow

This repository contains a GitHub Action workflow that allows you to create a new repository in the current organization based on an input repository URL.

### Usage

To use this GitHub Action workflow, follow these steps:

1. Trigger the workflow with the following input parameters:
   - `repoUrl`: The GitHub repository URL in HTTPS format.
   - `branchName`: The branch name to use (default value is `main`).

2. The workflow will perform the following steps:
   - Print out the input parameters values in the workflow log.
   - Validate that the `repoUrl` is a GitHub.com address and a HTTPS address. If not, the job will fail.
   - Validate that the `repoUrl` exists on GitHub and is publicly accessible. If not, the job will fail.
   - Extract the `orgName` and `repoName` from the `repoUrl`, and concatenate them to form another parameter `docs_repo_name` = `orgName_repoName`.
   - Check if the repository `docs_repo_name` exists using the GitHub API.
   - If the repository exists, delete it before creating a new one.
   - Create a new repository in the current organization with the name `docs_repo_name` using the GitHub API.
   - Check if the local directory already exists and remove it if it exists before cloning the repository.
   - Clone the source from `repoUrl` at the end of the workflow, using the `branchName` input parameter to specify which branch to clone.
   - Clean up the working directory on the runner before running any steps.

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

### Note

The workflow requires a `GITHUB_TOKEN` secret to authenticate and create the new repository.

### Error Handling

The `Create new repo` step includes error handling to ensure that the job fails if the repository creation fails. The `curl` command used to create the repository includes the `--fail` option to ensure it fails on error, and `|| exit 1` to exit the job if the repository creation fails.

Additionally, the workflow now includes validation to check if the repository `docs_repo_name` exists before attempting to create it. If the repository exists, it will be deleted before creating a new one. This ensures that the repository creation process does not fail due to an existing repository with the same name.

The workflow also includes validation to check if the `repoUrl` exists on GitHub and is publicly accessible. If the `repoUrl` does not exist or is not publicly accessible, the job will fail. This ensures that the repository creation process does not fail due to an invalid or inaccessible `repoUrl`.

### Additional Steps

The workflow now includes additional steps to activate the python environment, run the 'aise-cli' command, and parse the repo using 'aise-cli repo parse-repo':

1. Activate the python environment:
   ```shell
   source ~/source/aise-cli/.venv/bin/activate
   ```

2. Run the 'aise-cli' command to test if the python environment works:
   ```shell
   aise-cli
   ```

3. Delete the source repo and parse the repo using 'aise-cli repo parse-repo':
   ```shell
   # delete source repo
   aise-cli repo delete-repo source_repo
   # parse repo
   aise-cli repo parse-repo --repo_path  aise-cli repo parse-repo --repo_name <source_repo absolute>
   ```

### Created by code2repo ai

This repository was created by code2repo ai.
