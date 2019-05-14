# Development flow

The development of Annif follows [GitHub flow](https://guides.github.com/introduction/flow/). Some basic principles:

1. The `master` branch is always a working, deployable version of Annif.  The code on the `master` branch will eventually be released as the next release.
2. All development happens on feature branches. Feature branches are normally named according to the issue they are addressing: e.g. `issue267-cli-analyze-to-suggest` which implements the change specified in issue [#267](https://github.com/NatLibFi/Annif/issues/267).
3. Feature branches are merged via *pull requests*. Opening a pull request signals the other developers that the feature is ready to be discussed and eventually merged. Pull requests should be marked with [draft status](https://github.blog/2019-02-14-introducing-draft-pull-requests/) if the developer knows that the code is not yet ready for merging but wants to start discussion. Also, various checks (Travis tests, test coverage tools and static analyzer services) are run on pull requests and these may provide important feedback to the developer.
4. Feature branches should be deleted after the pull request has been merged.
5. A new release is made whenever some important changes have landed in `master`. Releases are intended to be frequent. See [[Release process]] for the details of making a release.

# Branches

At any time, these branches typically exist:
* the `master` branch
* feature branches under development
* experimental branches that are not under active development but which we don't want to delete in case the code is later needed

# Tags

Releases are tagged, e.g. `v0.40.0` and `v0.37.2`. The release tags are created using the `bumpversion` tool, not manually. See [[Release process]] for details.