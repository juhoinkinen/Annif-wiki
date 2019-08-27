For troubleshooting and discussion, check out the Annif-users -mailing list/forum. Also feel free to open GitHub-issues if need be.

# Docker Troubles

- Note that simple `docker run` or `docker-compose up` do not fetch a new version of an image, even if that existed in in a repository, but to use a more recent image than exists locally, you must do [`docker pull IMAGE_NAME`](https://docs.docker.com/engine/reference/commandline/pull/) or [`docker-compose pull`](https://docs.docker.com/compose/reference/pull/).
