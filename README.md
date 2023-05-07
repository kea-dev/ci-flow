# lakruzz/ci-flow

This repo holds a number of different Continious Integration flows to use in GitHub Actions.

Each flow has workflow file in `.github/workflows` which is currently disabled - if you want to test any of them, you can simply create a new repo - using this one as a tempale - and then rename the file extension of the flow you like from `._yml` to `.yml`.

Alternatively you can copy the file directly to you own repo's `.github/workflows` directory. Remember to change the file extension to `.yml` to enable it!

The point of this repo is to demonstrate a lot of different things you can do in GitHub Actions - especially when it comes to Continious Integration whish is essentially the process before deploy or relaes. That is; the verification needed to accept a change onto the default integration branch (usually `main` or `master`).

We will start out with simple approaches and then make them more and more advanced - you should get off when you thing you found the one that suits your temper.

### Minimialilst starter

YAML: [ci-minimal._yml](.github/workflows/ci-minimal._yml)
Documentation [ci-minimal.md](ci-minimal.md)

Overall, this workflow checks out the code, sets up Git user information, rebases the current branch against the default integration branch, runs a build (or actually a placeholder for it), and pushes the changes to the remote default branch. 

It's a very basic continuous integration process that triggers on _any_ branch that isn't `main` or `master`.

