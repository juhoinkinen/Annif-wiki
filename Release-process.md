How to make a new release of Annif.

1. Check that the `master` branch is in shape for a new release - tests are passing, no open issues etc.
2. Make sure your local `master` branch is up to date w.r.t. GitHub: `git checkout master; git pull`
3. Activate the pipenv with `pipenv shell` if you haven't already
4. Make a new version with bumpversion: `bumpversion minor` or `bumpversion patch`
5. Push the commit to GitHub: `git push`
6. Push the version tag too: `git push --tags`
7. Wait for Travis CI builds to complete. The version tag should trigger a distribution build that is uploaded to PyPI. Also it should trigger archiving on Zenodo.
8. In GitHub Releases tab, turn the tab into a release and add release notes.
9. Close the milestone corresponding to the release (and create a new one for the next release).
10. Update the wiki documentation to match features of the new release (e.g. new or updated backends)
11. Announce the release on annif-users and other channels (e.g. twitter) 