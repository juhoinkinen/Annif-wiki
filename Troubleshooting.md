For troubleshooting and discussion, check out the Annif-users -mailing list/forum. Also feel free to open GitHub-issues if need be.

# Docker Troubles

- Note that simple `docker run` or `docker-compose up` do not fetch a new version of an image, even if that existed in in a repository, but to use a more recent image than exists locally, you must do [`docker pull IMAGE_NAME`](https://docs.docker.com/engine/reference/commandline/pull/) or [`docker-compose pull`](https://docs.docker.com/compose/reference/pull/).

- When using Docker with Windows, the id-variables id -u and id -g will not work. Under Windows, Docker usually runs in its own virtual machine, and this virtual machine in turn is run by whoever installed it (and the permissions are set accordingly). You can try leaving the part with id-variables out when you've figured out how the permissions work in your setup...
