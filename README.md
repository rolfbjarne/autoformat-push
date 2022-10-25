# Autoformat (push results)

This is a GitHub Action to applies a patch found in an artifact to a pull
request, and pushes the results to the branch in question.

This action should be called when the "Autoformat code" action has completed on
a pull request (using the `workflow_run` trigger, and filtering on successful
pull requests).

The secret is required to push to pull requests from forks.

Ref: The [rolfbjarne/autoformat](https://github.com/rolfbjarne/autoformat) action.

Important: this action must be in the default branch (usually `main`) for it to have any effect.

## Usage

Add a new file named `autoformat-push.yml` in the `.github/workflows` directory in your repository:

```yaml
name: Autoformat code - push results
on:
  workflow_run:
    workflows: ["Autoformat code"]
    types:
      - completed

# This action needs the following permissions in order to push the results back to the original branch.
permissions:
  pull-requests: write
  contents: write

jobs:
  push-and-notify:
    name: Push autoformatted code and notify user
    runs-on: ubuntu-latest
    if: >
      github.event.workflow_run.event == 'pull_request' &&
      github.event.workflow_run.conclusion == 'success'
    steps:
      - name: 'Push autoformatted patch'
        uses: rolfbjarne/autoformat-push@v0.1
        with:
          githubToken: ${{ secrets.GITHUB_TOKEN }}
```

## Configuration

```yaml
uses: rolfbjarne/autoformat@v0.1
  with:
    # The committer's email for the commit
    git_user_email: 'autoformat@example.com'

    # The committer's name for the commit
    git_user_name: 'GitHub Actions Autoformatter'

    # If a comment should be added to the pull request if something was pushed (required)
    commentOnPullRequest: true

    # The comment to add to the pull request
    commentContents: '# :warning: Your code has been reformatted. :warning:\n\n'

    # The name of the artifact where the patch is stored
    artifact: 'autoformat'

    # The GitHub token to use when pushing the commit (required)
    githubToken: '<token>'
```
