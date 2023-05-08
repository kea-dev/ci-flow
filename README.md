# lakruzz/ci-flow

This repo holds a number of different Continious Integration flows to use in GitHub Actions.

Each flow has workflow file in `.github/workflows` which is currently disabled - if you want to test any of them, you can simply create a new repo - using this one as a template - and then rename the file extension of the flow you like from `._yml` to `.yml`.

Alternatively you can copy the file directly to you own repo's `.github/workflows` directory. Remember to change the file extension to `.yml` to enable it!

The point of this repo is to demonstrate a handful of different things you can do in GitHub Actions - especially when it comes to Continious Integration whish is the process before deploy or release. That is; the verification needed to accept a change onto the default integration branch (usually `main` or `master`).

We will start out with simple approaches and then make them more and more advanced - you should get off when you thing you found the one that suits your temper.

### My flow works like this

- Create an issue fore everything your wanna do - you can even do it from the command line - think of this as your ToDo list, where you merely writing your commit message _before_  you start hacking.
  ```shell
  gh issue create -t "Some title"  -b "The Body"
  ```
- When ready to work on the issue (e.g. #15) you can start the development like this
  ```shell
  gh issue develop 15 -c
  ```

The flow can vary a bit from this point - maybe you want to run a test every time you commit to the issue branch, maybe you want to integrate the issue branch into `main` or `master` immidiately or perhaps you ony wnat to integrate issue branches that has been marked ready for integration by a pull request. Let's look at different apperoaches.

### Minimalistic starter flow

**YAML**: [`ci-minimal._yml`](.github/workflows/ci-minimal._yml)<br/>
**Documentation**: [`ci-minimal.md`](ci-minimal.md)

Overall, this workflow checks out the code, sets up Git user information, rebases the current branch against the default integration branch, runs a build (or actually a placeholder for it), and pushes the changes to the remote default branch. 

It's a very basic continuous integration process that triggers on _any_ branch that isn't `main` or `master`.


### Optimized, safer, but still quite simple

**YAML**: [`ci-ff-only._yml`](.github/workflows/ci-ff-only._yml)<br/>
**Documentation**: [`ci-ff-only.md`](ci-ff-only.md)

Overall, this workflow achieves exactly the same as the previous `ci-minimal._yml` but it uses a few optimizations. And it's just a tad safer.

### Utilizing GitHub's Pull Requests (PRs) to automate flow

**YAML**: [`ci-pr._yml`](.github/workflows/ci-pr._yml)<br/>
**Documentation**: [`ci-pr.md`](ci-pr.md)

This workflow doesn't trigger on branches, but uses pull requests instead. The PR's branch is still rebased first, tested and then delivered to GitHub. The result is the same, but the workprocess differes; Nothing is tested until the developer explicitly makes the issue brach as _ready_ by creating a pr on the issue branch.

In this flow we imagin that the process is extended with an extra actions. When you are _ready_ to integrate (you are rebased, you code works - you know it because you have tested it) you create a pull request (be sure to push the branch to GitHub and that it's checked out in yout repo)
```shell
gh pr create --fill
```

# Optimize more
There - now you have a few basic principles to go with. There is still a lot of optimizations you can do. You should _continiously optimize_ you flow, but consider, when you work on flows, to take it to a seperate repo. I tend to find that is often requires quite a few commits to cover all corner-cases. When you have your flow working in your test repo, you copy it back to your production repo.
