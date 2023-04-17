How to make a new release of Annif. 

## Normal release (minor version)
1. Check that the `main` branch is in shape for a new release - tests are passing, no serious open issues etc.
2. Make sure your local `main` branch is up to date w.r.t. GitHub: `git checkout main; git pull`
3. Activate the virtual environment with `poetry shell` if you haven't already
4. Update the release-date in CITATION.cff: `sed -ri "s/date-released\:\s[[:digit:]]{4}-[[:digit:]]{2}-[[:digit:]]{2}/date-released: $(date '+%Y-%m-%d')/g" CITATION.cff; git diff CITATION.cff` and commit: `git add CITATION.cff; git commit -m "Update release-date"`
5. Make a new version with bumpversion: `bumpversion release`
6. Check with `git log` that the new version number matches your expectations.
7. Push the commit to GitHub: `git push`
8. Push the version tag too: `git push --tags`
9. Wait for [GitHub Actions jobs](https://github.com/NatLibFi/Annif/actions) to complete. The version tag should trigger a distribution build that is uploaded to [PyPI](https://pypi.org/project/annif/).
10. In GitHub [Releases](https://github.com/NatLibFi/Annif/releases) tab, turn the tag into a release and add release notes. This should trigger archiving on [Zenodo](https://doi.org/10.5281/zenodo.2578948).
11. Close the milestone corresponding to the release (and create a new one for the next release).
12. Update the wiki documentation to match features of the new release (e.g. new or updated backends) and check that [API documentation](https://annif.readthedocs.io/en/latest/index.html) has been correctly updated (updating should happen on all commits to `main`).
13. Announce the release on [annif-users](https://groups.google.com/forum/#!forum/annif-users) and other channels (e.g. twitter) 
14. Create a blog post (in Finnish) in the [Finto wiki](https://www.kiwi.fi/display/Finto/Tervetuloa) about the new release (make sure to include at least the label `finto`, preferably also `fintoai`)
15. Announce the release on [@Fintopalvelu](https://twitter.com/Fintopalvelu) twitter, with a link to the blog post
16. Post the announcement to the mailing lists `finto-tiedotus` and `automaattinen-kuvailu` (make sure the message is not held up in moderation)
17. Prepare the `main` branch for the next development release with bumpversion: `bumpversion --no-tag minor` (this should increment the second part of the version number and add a `-dev` suffix, but not create a new tag, as it would create confusion e.g. on PyPI)
18. Check with `git log` that the new version number matches your expectations.
19. Push the commit to GitHub: `git push`

## Patch release
1. Checkout the previous release tag, e.g. `git checkout v0.54.0`
2. Create and checkout a new branch for the new release, e.g. `git checkout -b release-0.54.1`
3. Include the wanted changes to the branch by cherry-picking commits or merging branches.
4. Update the release-date in CITATION.cff as in step 4 of normal release.
5. Make a new *patch* version with bumpversion: the new version needs to be set manually, e.g.: `bumpversion --new-version 0.54.1 patch`
6. Follow the normal release process from step 6 as needed.
7. If necessary, change the GitHub milestones of the PRs included in this patch to point to the correct minor version (in case the milestones have originally been set to next minor release, e.g. 0.55).