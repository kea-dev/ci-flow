# `ci-pr.yml`

##### This workflow doesn't trigger on branches, but uses pull requests instead. The PR's branch is still rebased first, tested and then delivered to GitHub. The result is the same, but the workprocess differes; Nothing is tested until the developer explicitly makes the issue brach as _ready_ by creating a pr on the issue branch.

[<< BACK to README.md](README.md)



### Event Triggers
The workflow is triggered on pull requests that are meant for either `main` or `master` branch. 

```yaml
on:
  pull_request:
    branches:
      - main
      - master
```


### Permissions
The permissions are updated to include write access to pull-request. 

```yaml
permissions:
  contents: write
  pull-requests: write
```

#### Get the Default Target Branch and rebase against it
This step still uses the `gh` command-line tool to determine the repository's default branch. But this time the setting of the `target_branch` variable is using the [new standard approach](https://github.blog/changelog/2022-10-11-github-actions-deprecating-save-state-and-set-output-commands/) recommended by GitHub. Consequently the rebase step which uses the variable is also changed accordingly.

```yaml
- name: Get default target branch name
  id: repo
  run: |
    set -ex
    echo "target_branch=$( gh repo view --json defaultBranchRef --jq .defaultBranchRef.name )" >> "$GITHUB_ENV"

- name: Rebase against default branch
  run: |
    set -ex
    git rebase ${ORIGIN}/${{ env.target_branch }}
```



#### Push Changes to Default Target Branch
This step uses the `gh` command-line tool to merge the pr using using the rebase merge strategy. It also cleans up the branch on github. which is neat, since a clent can now use the `--prune` switch to either `git pull` og `git fetch` to clean out local obsolete branches.

```yaml
- name: Merge and delete branch, close PR
  run: |
    set -ex
    gh pr merge --auto --rebase --delete-branch --subject "Automated merge by ${{ github.job }}"
```

[<< BACK to README.md](README.md)

