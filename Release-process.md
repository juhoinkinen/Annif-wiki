How to make a new release of Annif. 

## Normal release (minor version)
1. Check that the `master` branch is in shape for a new release - tests are passing, no serious open issues etc.
2. Make sure your local `master` branch is up to date w.r.t. GitHub: `git checkout master; git pull`
3. Activate the virtual environment with `source venv/bin/activate` if you haven't already
4. Make a new version with bumpversion: `bumpversion release`
5. Check with `git log` that the new version number matches your expectations.
6. Push the commit to GitHub: `git push`
7. Push the version tag too: `git push --tags`
8. Wait for [GitHub Actions jobs](https://github.com/NatLibFi/Annif/actions) to complete. The version tag should trigger a distribution build that is uploaded to [PyPI](https://pypi.org/project/annif/).
9. In GitHub [Releases](https://github.com/NatLibFi/Annif/releases) tab, turn the tag into a release and add release notes. This should trigger archiving on [Zenodo](https://doi.org/10.5281/zenodo.2578948).
10. Close the milestone corresponding to the release (and create a new one for the next release).
11. Update the wiki documentation to match features of the new release (e.g. new or updated backends) and check that [API documentation](https://annif.readthedocs.io/en/latest/index.html) has been correctly updated (updating should happen on all commits to `master`).
12. Announce the release on [annif-users](https://groups.google.com/forum/#!forum/annif-users) and other channels (e.g. twitter) 
13. Create a blog post (in Finnish) in the [Finto wiki](https://www.kiwi.fi/display/Finto/Tervetuloa) about the new release (make sure to include a Label `finto`)
14. Announce the release on [@Fintopalvelu](https://twitter.com/Fintopalvelu) twitter, with a link to the blog post
15. Post the announcement to the mailing lists `finto-tiedotus` and `automaattinen-kuvailu` (make sure the message is not held up in moderation)
16. Prepare the `master` branch for the next development release with bumpversion: `bumpversion --no-tag minor` (this should increment the second part of the version number and add a `-dev` suffix, but not create a new tag, as it would create confusion e.g. on PyPI)
17. Check with `git log` that the new version number matches your expectations.
18. Push the commit to GitHub: `git push`

## Patch release
1. Checkout the previous release tag, e.g. `git checkout v0.54.0`
2. Create and checkout a new branch for the new release, e.g. `git checkout -b release-0.54.1`
3. Include the wanted changes to the branch by cherry-picking commits or merging branches.
4. Make a new *patch* version with bumpversion: the new version needs to be set manually, e.g.: `bumpversion --new-version 0.54.1 patch`
5. Follow the normal release process from step 5 as needed ("Check with `git log`"...)
6. If necessary, change the GitHub milestones of the PRs included in this patch to point to the correct minor version (in case the milestones have originally been set to next minor release, e.g. 0.55).