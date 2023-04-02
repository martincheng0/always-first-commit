# Always First Commit for GitHub Actions

This GitHub Action clears all previous history of a repository and creates a single commit to the repo. This is useful when you want to create a new repository with the same content as an existing one, but without any previous history.

## Usage:

Before using this action, you need to create a GitHub Personal Access Token (PAT) and store it as a secret in your repository. The PAT is required to authenticate with the GitHub API to access the existing and target repositories that you own.

- Follow the instructions [here](https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/creating-a-personal-access-token#creating-a-fine-grained-personal-access-token) to create a PAT

  - The PAT needs to at least have the permission of `Read and Write access to code`
  - The PAT needs permission to access both the **existing** and **target** repositories

- Follow the instructions [here](https://docs.github.com/en/actions/security-guides/encrypted-secrets#creating-encrypted-secrets-for-a-repository) to store the PAT as a secret in your repository
  - Name the secret as `PAT_GITHUB`

To use this action, add the following to your workflow file:

```yaml
- name: Always First Commit
  uses: martincheng0/always-first-commit@main
  with:
    user_name: <git username or leave it empty>
    user_email: <git email or leave it empty>
    owner: ${{ github.repository_owner }}
    repo: <target repo>
    branch: <target branch>
    commit_message: <commit message>
    exclude_files: <files to be excluded or leave it empty>
    exclude_folders: <folders to be excluded or leave it empty>
  secrets:
    token: ${{ secrets.PAT_GITHUB }}
```

## Caveats

When using this action, keep the following caveats in mind:

- This action permanently removes all previous history of the specified branch in the target repository. This means that all previous commits, branches, and tags will be lost. This can cause confusion and make it difficult for other contributors to understand the changes that have been made to the repository.

- Using the this action in a repository where you collaborate with others can cause issues and should be avoided. It is best to use it only in repositories where you are the sole contributor

Example
Here's an example of a workflow file that uses this action:


```yaml
name: Always First Commit

on:
  push:
    branches:
      - main

- name: Always First Commit
  uses: martincheng0/always-first-commit@main
  with:
    owner: ${{ github.repository_owner }}
    repo: always-first-commit-demo
    branch: main
    commit_message: Initial commit
    exclude_files: .github/workflows/workflow.yml
  secrets:
    token: ${{ secrets.PAT_GITHUB }}
```

This workflow clears the history of the `main` branch of the `always-first-commit-demo` repository, adds all the content except `.github/workflows/workflow.yml` as a new commit with the message `"Initial commit"`, and pushes the changes to the remote repository using the existing Git username and email
