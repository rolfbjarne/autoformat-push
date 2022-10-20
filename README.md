# Autoformat (push results)

This is a GitHub Action to applies a patch found in an artifact to a pull
request, and pushes the results to the branch in question.

This action should be called when the "Autoformat code" action has completed on
a pull request (using the `workflow_run` trigger, and filtering on successful
pull requests).

The secret is required to push to pull requests from forks.

Ref: The [rolfbjarne/autoformat](https://github.com/rolfbjarne/autoformat) action.

## Usage

Add a new file named `autoformat.yml` in the `.github/workflows` directory in your repository:

```yaml
name: Autoformat code - push results
on:
  workflow_run:
    workflows: ["Autoformat code"]
    types:
      - completed

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
