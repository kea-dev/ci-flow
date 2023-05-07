# `ci-ff-only.yml`

##### Overall, this workflow achieves exactly the same as the [`ci-minimal._yml`](ci-minimal.md) but it uses a few optimizations.  Let's go through what is differet.

[<< BACK to README.md](README.md)

### Jobs
The workflow defines a single job named `ci` that runs on an Ubuntu environment.

```yaml
jobs:
  ci:
    runs-on: ubuntu-latest
```

### Steps
The `ci` job consists of several steps that are executed sequentially:

#### Get default target branch name
This step is added - the step has an id, and the step sets an output-parameter. This is how GitHub Actions enables steps to share information between one and another.

When a step has an id, and sets and output, other steps can access that using

```shell
${{ steps.<step_id>.outputs.<output_name> }}
```

```yaml
- name: Get default target branch name
  id: repo
  run: |
    set -ex
    TARGET_BRANCH=$( gh repo view --json defaultBranchRef --jq .defaultBranchRef.name )
    echo ::set-output name=target_branch::$TARGET_BRANCH
````


#### Rebase Against Default Target Branch
This step does the same as before, only this time it doesn't execute the `gh` CLI command, it simply reuses the `target_branch` name retrived in the step before.

```yaml
- name: Rebased against default target branch
  run: |
    set -ex
    git rebase ${ORIGIN}/${{ steps.repo.outputs.target_branch }}
```

#### Checkout default branch, merge only if Fast-Forward and Push back to origin
The previous flow simply pushed changes back to the defaould target branch. But now the step is updated to even handle any changes to the default branch that might have accurred during the time it toot kto veryfy the branch.

It checks out the target branch, updates with a pull, then merges the issue branch into it making sure that only Fast-Forward merges are allowed - and then pushes the result back to origin (GitHub). Last time we had re-executed the `gh` CLI command to get the repo's defaul tranch, but this time we simply reuse the information from the earlier step that retrived it once and for all.

```yaml
- name: Checkout default branch, merge only if Fast-Forward and Push back to origin
  run: |
    set -ex
    git checkout ${{ steps.repo.outputs.target_branch }}
    git pull
    git merge --ff-only ${{github.ref_name}}
    git push ${ORIGIN} ${{ steps.repo.outputs.target_branch }}
```

[<< BACK to README.md](README.md)

