# `ci-minimal.yml`

##### Overall, this workflow checks out the code, sets up Git user information, rebases the current branch against the default branch, runs a build (or a placeholder for it), and pushes the changes to the default branch. It's a basic continuous integration process that can be customized based on the needs of the repository.

[<< BACK to README.md](README.md)

Specifically this is a  YAML file that defines a GitHub Actions workflow named "Continuous Integration". Here's what the workflow does:

### Event Triggers
The workflow is triggered when a push event occurs on any branch other than the main branch. 

```yaml
on:
  push:
    branches-ignore:
      - main
```

### Environment Variables
The workflow defines two environment variables:

1. `GITHUB_TOKEN`: A secret token used to authenticate the workflow with GitHub. This variable is created automatically by GitHub and is available as a default secret. When set as an environment variable `GITHUB_TOKEN`it's used to authenticate the `gh` CLI calls. 
2. `ORIGIN`: The name of the remote repository. 

```yaml
env:
  GITHUB_TOKEN: ${{secrets.GITHUB_TOKEN}}
  ORIGIN: origin
```

### Permissions
The workflow requires write permissions for the contents of the repository. This is necessary since the workflow will push back changes to the repo if everything succeeds. 

```yaml
permissions:
  contents: write
```

### Jobs
The workflow defines a single job named `ci` that runs on an Ubuntu environment.

```yaml
jobs:
  ci:
    runs-on: ubuntu-latest
```

### Steps
The `ci` job consists of several steps that are executed sequentially:

#### Checkout Code
This step uses the `actions/checkout` action to checkout the repository's code. `fetch-depth` fecthec the entire history of the repo, as opposed to to depth=1, which is the default for `actions/checkout`. The full repository history is required since we will be doing a rebase later - an operation whish is not allowd on a shallow repo. 

```yaml
- name: Checkout code
  uses: actions/checkout@v3
  with:
    fetch-depth: 0
```

#### Tell Git Who We Are
This step sets the Git user name and email to "GitHub Actions" and "github-actions@github.com", respectively. Setting `advice.skippedCherryPicks` to false is just for convenience - if not set, the rebase command will warn that the default behaviour of rebase may ship some unimportant commits - but this is exactly what we want, so we don't want the warning.

```yaml
- name: Tell git who we are
  run: |
    set -ex
    git config --global user.name "GitHub Actions"
    git config --global user.email "github-actions@github.com"
    git config --global advice.skippedCherryPicks false
```

#### Rebase Against Default Target Branch
This step uses the `gh` command-line tool to determine the repository's default branch and rebase the current branch against it.

```yaml
- name: Rebased against default target branch
  run: |
    set -ex
    TARGET_BRANCH=$( gh repo view --json defaultBranchRef --jq .defaultBranchRef.name )
    git rebase ${ORIGIN}/${TARGET_BRANCH}
```

#### Build
This step is a placeholder that prints a message to the console to indicate where to run the actual build or what ever quality chaeck you want to perform in order to let code in on the default target branch.

```yaml
- name: Do the build
  run: |
    echo This is where you run the build
```

#### Push Changes to Default Target Branch
This step uses the `gh` command-line tool (again) to determine the repository's default branch and push the changes directly to the remot branch.

```yaml
- name: Push changes to default target branch
  run: |
    set -ex
    TARGET_BRANCH=$( gh repo view --json defaultBranchRef --jq .defaultBranchRef.name )
    git push ${ORIGIN} ${{github.ref_name}}:${TARGET_BRANCH}
```

[<< BACK to README.md](README.md)

