# Coding Standards

## Project Structure

Smalltalk projects can use the [GitHub Setup Utility](https://github.com/ba-st/GitHub-setup) to draft new projects following the ecosystem conventions. The proposed structure is:

- `assets/` Static resources
  - `logos/`  Project logos
- `docs/` Documentation
  - `Installation.md` Installation instructions
- `source/` Smalltalk source code
- `README.md`
- `CONTRIBUTING.md` Contribution guidelines
- `LICENSE` Source code license, MIT is preferred

### General Settings

- Go to `Settings -> Options`
  - In the `Features` section
    - Disable `Wikis`
    - Check `Issues` and `Projects`
  - In the `Merge button` section
    - Disable `Allow squash commits`
    - Disable `Allow rebase merging`

### Issue Labels

- Open a Pharo 7 image
- Copy [this code](https://gist.github.com/gcotelli/cd3f3e50faeb37325e8f0eb9288fdb1f) in a Playground
- Change `REPO`, `OWNER`, `USER` and `PASSWORD`
  - If you don't want to use your password, or have 2 factor authentication enabled, instead of password you can use an access token with `repo` scope. This can be generated in https://github.com/settings/tokens.
- `Do it` to get the issue labels configured by using the GitHub API


## Source Code

- The Smalltalk source code must be in [Tonel](https://github.com/pharo-vcs/tonel) format
- The Smalltalk source code must be located in the `source` folder

### Code formatting

- Open a Pharo image
- For Pharo 7/8 copy [this code](https://gist.github.com/gcotelli/54d3648c656cb2ac79872c0932c424b6) in a Playground
- For Pharo 9+ copy [this code](https://gist.github.com/gcotelli/08730091ba8a4fe0e557aeca489ebde8) in a Playground
- `Do it` to configure the formatter

### Baseline groups

The baseline must include the following groups:

- `Deployment` must include all the packages strictly needed in a production environment
- `Tests` must include the tests packages
- `Tools` must include the packages including development tools (eg. inspector extensions, spotter extensions, specific dev tools per se)
- `Dependent-SUnit-Extensions` must include extensions to the `SUnit` framework, intended to be used not only in this project but also on dependent projects (eg. `TestAsserter` extension methods, abstract `TestCase` subclasses)
- `CI` the group loaded in the continuous integration jobs, by default equal to `Tests`
- `Development` by default `Tests` + `Tools`, all the packages needed to develop the project

Don't forget to use `MetacelloCypressBaselineProject` as the `projectClass` in the baseline.

### Package Naming

- Test packages must be named like the package under test with `-Tests` as suffix.

## Development Workflow

- The default branch must be `release-candidate`, and it must be always in a releasable state.
- Released versions are managed using tags and must be fixed to a specific commit and never changed.
- These tags must be of the form `v{MAJOR}.{MINOR}.{PATCH}` following the semantic versioning conventions.
- For each major version a branch of the form `v{MAJOR}` must be mantained
- The `release-candidate` and version branches must be protected
- New features must be developed in a branch originated from `release-candidate` and merged back to it using a pull request (by creating a merge commit), after the status checks are Ok and the code review is approved.
- Bug fixes can originate from `release-candidate` or the specific major version branch, and must follow a similar approach using pull requests. In case a bug fix starts from a major version branch, a PR against `release-candidate` must be produced (if applies).

### How to configure the Default Branch
- Go to `Settings -> Branches` in your GitHub project
- Select `release-candidate` in the `Default branch` section

### How to protect a branch
- Go to `Settings -> Branches` in your GitHub project
- Click on `Add rule`
- In `Apply rule to` section input the branch name
- In `Rule settings` check:
  - `Require pull request reviews before merging`
    - `Dismiss stale pull request approvals when new commits are pushed`
  - `Require status checks to pass before merging`
    - Status checks for Travis-CI/GitHub actions and codecov
  - `Restrict who can push to matching branches`
- Click on `Create`

## Releasing a new version

If the changes don't break backwards compatibility:
- Issue a pull request with `release-candidate` as the source, targeted to the last major version branch
- After all the checks have passed and the code review is performed
  - If the pull request can be fast-forwarded, merge it with:
  ```
  git fetch origin
  git checkout release-candidate
  git pull
  git checkout v{MAJOR}
  git branch --set-upstream-to=origin/v{MAJOR} v{MAJOR}
  git pull
  git merge release-candidate
  git push origin v{MAJOR}
  ```
  - If the pull request cannot be fast-forwarded use the `Merge pull request` button on the GitHub web interface.

If the changes break backwards compatibility:
- Create the new major branch
```
git checkout release-candidate
git pull
git checkout -b v{MAJOR}
git push origin v{MAJOR}
```

Once the major branch is updated, tag this commit with the corresponding version, or use the GitHub releases web ui to create a release at the correspoding commit.
