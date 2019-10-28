How to make a new release of Annif.

1. Check that the `master` branch is in shape for a new release - tests are passing, no serious open issues etc.
2. Make sure your local `master` branch is up to date w.r.t. GitHub: `git checkout master; git pull`
3. Activate the pipenv with `pipenv shell` if you haven't already
4. Make a new version with bumpversion: `bumpversion minor` or `bumpversion patch`
5. Check with `git log` that the new version number matches your expectations.
6. Push the commit to GitHub: `git push`
7. Push the version tag too: `git push --tags`
8. Wait for [Travis CI builds](https://travis-ci.org/NatLibFi/Annif) to complete. The version tag should trigger a distribution build that is uploaded to [PyPI](https://pypi.org/project/annif/).
9. In GitHub [Releases](https://github.com/NatLibFi/Annif/releases) tab, turn the tag into a release and add release notes. This should trigger archiving on [Zenodo](https://doi.org/10.5281/zenodo.2578948).
10. Close the milestone corresponding to the release (and create a new one for the next release).
11. Update the wiki documentation to match features of the new release (e.g. new or updated backends) and check that [API documentation](https://annif.readthedocs.io/en/latest/index.html) has been correctly updated (updating should happen on all commits to `master`).
12. Announce the release on [annif-users](https://groups.google.com/forum/#!forum/annif-users) and other channels (e.g. twitter) 