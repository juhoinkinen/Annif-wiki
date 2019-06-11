# Install Docker

To be able to use Docker in your system, you need to have installed Docker-engine. Step-by-step instructions for this for Windows, Mac, and Linux distributions can be found in [Docker documentation](https://docs.docker.com/install/). 

# Running Annif in Docker container

In case you are using Linux, you can start bash shell in a container based on the Annif image with:

    docker run -it annif bash 

If this is the first time you are using the Annif image, the image will now be downloaded from [Docker Hub](https://hub.docker.com/). A bash shell starts in the container, where it is possible to run Annif [[Commands]] (here the `-it` flag is for enabling interactive mode). The container can be exited with `exit`.

However, the Annif image itself does not contain any vocabulary or training data. A directory containing these can be bind mounted as a [volume](https://docs.docker.com/storage/volumes/) from the host file system to the container using the syntax `-v /absolute_path/on/host:/path/in/container` after the `docker run` command<sup id="a1">[1](#myfootnote1)</sup>. Also, the user in a docker container is by default not the same as on the host system and any file created in a container is not owned by the host user, and with bind-mounts this can lead to issues with file permissions. Therefore it is best to make [the user in the container](https://docs.docker.com/engine/reference/run/#user) the same as on the host, using `-u $(id -u):$(id -g)`. With these flags the command to run bash in a container with Annif looks like this:

    docker run \
        -v ~/annif-projects:/annif-projects \
        -u $(id -u):$(id -g) \
        -it annif bash

Here the `annif-projects/` directory is assumed to exist in home directory on host (and it is mounted with the same name on the root of the container filesystem). From here on the post-installation steps for using Annif in [[Getting Started]] can be followed. 

Specifically, the template configuration file [`projects.cfg.dist`](https://github.com/NatLibFi/Annif/blob/master/projects.cfg.dist) can be placed to `~/annif-projects/` in the host system with the name `projects.cfg` along the vocabulary and training data (e.g. [Annif-corpora](https://github.com/NatLibFi/Annif-corpora)).

*Note that any data should not be stored in other locations in the container but in the mounted directory*, as after the container has stopped, [it is not convenient to gain access to the data again](https://docs.docker.com/engine/reference/commandline/commit/).

If the web UI started by `annif run` is used from within the container, also the flag `--network="host"` [needs to be included in the `docker run` command](https://docs.docker.com/engine/reference/run/#network-host).

# Running Annif as HTTP server with Gunicorn and NGINX

The web UI can also run on Gunicorn and NGINX. For this, you can use [docker-compose](https://docs.docker.com/compose/). For accessing the configuration and data files an environment variable can be set for the docker run: 

`ANNIF_PROJECTS=path/to/annif-projects docker-compose up`

This sets up containers according to [`docker-compose.yml`](https://github.com/NatLibFi/Annif/blob/issue278-dockerize-annif/docker-compose.yml), which in this case instructs docker to start separate containers for a Gunicorn server running Annif and for NGINX. The NGINX configuration is in [`nginx.conf`](https://github.com/NatLibFi/Annif/blob/issue278-dockerize-annif/annif/nginx/nginx.conf). 

# Connecting to Mauiservice in Docker container

The [Maui backend](https://github.com/NatLibFi/Annif/wiki/Backend%3A-Maui) can be used by running [Maui service](https://github.com/NatLibFi/mauiservice/blob/dockerize-mauiservice/DEVELOPER.md#usage-with-docker) in a separate container and connecting to it from Annif. For this both Annif and Mauiservice containers need to be started with `--network="host"` flag.

# Using Docker in Annif development

It is possible to mount also the Annif source code into the container, which allows editing the code in the host system while running Annif and tests (included in the `annif-dev` image) in the container:

    docker run \
        -v ~/annif-projects:/annif-projects \
        -v $(pwd):/Annif \
        -u $(id -u):$(id -g) \
        -it annif-dev bash

Here it is assumed that the current working directory is the one containing the source code (thus the use of `$(pwd)`).

&nbsp;

<a name="myfootnote1">1</a>:
Alternatively, it is possible to create and bind a [named volume](https://success.docker.com/article/different-types-of-volumes), which initially is empty, and get data into it by [copying](https://docs.docker.com/engine/reference/commandline/cp/) from host or fetching from internet, e.g. using wget in a running container to dowload [Annif-corpora Git Hub page](https://github.com/NatLibFi/Annif-corpora):

`wget -O - https://github.com/NatLibFi/Annif-corpora/tarball/master | tar xz`


