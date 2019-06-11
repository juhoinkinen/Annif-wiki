# Install Docker

To be able to use Docker in your system, you need to have installed Docker-engine. Step-by-step instructions for this for Windows, Mac, and Linux distributions can be found in [Docker documentation](https://docs.docker.com/install/). 

# Running Annif in Docker container

In case you are using Linux, you can start bash shell in a container based on the Annif image with:

    docker run -it annif bash 

If this is the first time you are using the Annif image, the image will now be downloaded from [Docker Hub](https://hub.docker.com/). A bash shell starts in the container, where it is possible to run Annif [[Commands]] (here the `-it` flag is for enabling interactive mode).

However, the Annif image itself does not contain any vocabulary or training data. A directory containing these can be bind mounted as a [volume](https://docs.docker.com/storage/volumes/) from the host file system to the container using the syntax `-v /absolute_path/on/host:/path/in/container` after the `docker run` command<sup id="a1">[1](#myfootnote1)</sup>. To bind mount the current working directory on host, `$(pwd)` can be used instead explicitly writing the absolute path. Also, the user in a docker container is by default not the same as on the host system and any file created in a container is not owned by the host user, and with bind-mounts this can lead to issues with file permissions. Therefore it is best to make [the user in the container](https://docs.docker.com/engine/reference/run/#user) the same as on the host, using `-u $(id -u):$(id -g)`. With these two flags the command to run bash in a container with Annif looks like this:

    docker run \
        -v $(pwd):/annif-projects \
        -u $(id -u):$(id -g) \
        -it annif bash

Now the directory where the command was issued on host is mounted with name `annif-projects` on the root of the container filesystem, and the post-installation steps in [[Getting Started]] can be followed. 

Specificly, the template configuration file comes with the Annif image and can be copied to the `/annif-projects` directory:

    cp /Annif/projects.cfg.dist  /annif-projects/projects.cfg

The vocabulary and training data (e.g. [Annif-corpora](https://github.com/NatLibFi/Annif-corpora)) can be fetched using the host system to the mounted directory.

*Note that any data should not be stored in other locations in the container but in the mounted directory*, as after the container has stopped, [it is not convenient to gain access to the data again](https://docs.docker.com/engine/reference/commandline/commit/).

If the web UI started by `annif run` is used from within the container, also the flag `--network="host"` [needs to be included in the `docker run` command](https://docs.docker.com/engine/reference/run/#network-host).

# Running Annif as HTTP server with Gunicorn and NGINX

The web UI can be used also run on Gunicorn and NGINX. For this, you can use [docker-compose](https://docs.docker.com/compose/). For accessing the configuration and data files an environment variable can be set: 

`export ANNIF_PROJECTS=path/to/annif-projects`

Then the services can be started by running

`docker-compose up`

in `Annif/`. This sets up containers according to [`docker-compose.yml`](https://github.com/NatLibFi/Annif/blob/issue278-dockerize-annif/docker-compose.yml), which in this case instructs docker to start separate containers for a Gunicorn server running Annif and for NGINX. The NGINX configuration is in [`nginx.conf`](https://github.com/NatLibFi/Annif/blob/issue278-dockerize-annif/annif/nginx/nginx.conf). 

&nbsp;

<a name="myfootnote1">1</a>:
Alternatively, it is possible to create and bind a [named volume](https://success.docker.com/article/different-types-of-volumes), which initially is empty, and get data into it by [copying](https://docs.docker.com/engine/reference/commandline/cp/) from host or fetching from internet, e.g. using wget in a running container to dowload [Annif-corpora Git Hub page](https://github.com/NatLibFi/Annif-corpora):

`wget -O - https://github.com/NatLibFi/Annif-corpora/tarball/master | tar xz`


