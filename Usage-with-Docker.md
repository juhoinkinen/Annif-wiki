# Install Docker

To be able to use Docker in your system, you need to have installed Docker-engine. Step-by-step instructions for this for Windows 10, MacOS, and Linux distributions can be found in [Docker documentation](https://docs.docker.com/install/). 

# Running Annif in Docker container

These instructions have been written for Linux use, but most of them should work also when using Windows or MacOS. In Windows you should use a Command Prompt or PowerShell terminal window for entering the commands. 

"Installation" is very easy: The following command will download the Docker image for Annif from quay.io registry  (if the image does not yet exist locally) and start the Bash shell in a container:

    docker run -it quay.io/natlibfi/annif bash 

In the shell it is possible to run Annif [[Commands]] (here the `-it` option is for enabling interactive mode). The container can be exited with `exit` command. (Note that by default Docker image with the `latest` tag is used, which in case of Annif is build on the current `master` git branch; to use an image of a specific release, append the image name with a colon and the release version number, use e.g. `quay.io/natlibfi/annif:0.42`. The first release with Docker image is 0.42) 

However, the Annif image itself does not contain any vocabulary or training data. A directory containing these can be [bind mounted](https://docs.docker.com/storage/bind-mounts/) from the host file system to the container using the syntax `-v /absolute_path/on/host:/path/in/container` after the `docker run` command. (Alternatively, it is possible to create and mount a [named volume](https://success.docker.com/article/different-types-of-volumes), which initially is empty, and get data into it by [copying](https://docs.docker.com/engine/reference/commandline/cp/) from host or fetching from internet, e.g. using wget in a running container to dowload [Annif-corpora Git Hub page](https://github.com/NatLibFi/Annif-corpora).

Also, the user in a docker container is by default not the same as on the host system and any file created in a container is not owned by the host user, and with bind mounts this can lead to problems with file permissions. Therefore it is best to make [the user in the container](https://docs.docker.com/engine/reference/run/#user) the same as on the host, using `-u $(id -u):$(id -g)` ([in Windows this is not possible](https://docs.docker.com/docker-for-windows/faqs/#can-i-change-permissions-on-shared-volumes-for-container-specific-deployment-requirements) and this option can be omitted). 

With the bind-mount and user-setting options the command to run bash in a container with Annif looks like this:

    docker run \
        -v ~/annif-projects:/annif-projects \
        -u $(id -u):$(id -g) \
        -it quay.io/natlibfi/annif bash

Here the `annif-projects/` directory is assumed to exist in home directory on host (and it is mounted with the same name on the root of the container filesystem). From here on the post-installation steps for using Annif in [[Getting Started]] can be followed. 

Specifically, the template configuration file [`projects.cfg.dist`](https://github.com/NatLibFi/Annif/blob/master/projects.cfg.dist) can be placed to `~/annif-projects/` in the host system with the name `projects.cfg` along the vocabulary and training data (e.g. [Annif-corpora](https://github.com/NatLibFi/Annif-corpora)).

*Note that any data should not be stored in other locations in the container but in the mounted directory*, as after the container has stopped, [it is not convenient to gain access to the data again](https://docs.docker.com/engine/reference/commandline/commit/).

If the web UI started by `annif run` is used from within the container, also the option `--network="host"` [needs to be included in the `docker run` command](https://docs.docker.com/engine/reference/run/#network-host).


# Using Annif with Gunicorn, NGINX, and Maui backend
Different containerized services can be conveniently linked together by using [docker-compose](https://docs.docker.com/compose/). The instructions to set up the services are in [`docker-compose.yml`](https://github.com/NatLibFi/Annif/blob/master/docker-compose.yml), which in this case instructs docker to start separate containers for 
* bash shell to run  Annif commands
* Gunicorn server running Annif Web UI
* [NGINX proxy server](https://www.nginx.com/resources/wiki/)
* [Maui Server](https://github.com/NatLibFi/MauiServer) to access [Maui backend](https://github.com/NatLibFi/Annif/wiki/Backend%3A-Maui)

To start these services, while in a directory where the `docker-compose.yml` is (download the file or whole Annif  repository), run 

    ANNIF_PROJECTS=~/annif-projects MY_UID=$(id -u) MY_GID=$(id -g) docker-compose up

Here the environment variables are needed for mounting the directory for vocabulary and training data files and setting the user in the container the same as on the host. In Windows setting these variables should be omitted and the lines including `MY_UID` and `MY_GID` in [`docker-compose.yml`](https://github.com/NatLibFi/Annif/blob/master/docker-compose.yml) removed, and there also the path of the directory to be mounted should be directly given in place of `${ANNIF_PROJECTS}` (e.g. `c:/users/example.user/annif/annif-projects/`). Once the services have started, the Annif web UI is accessible at http://localhost/ run by NGINX (see [this](https://docs.docker.com/docker-for-windows/troubleshoot/#limitations-of-windows-containers-for-localhost-and-published-ports) in case of problems for accessing localhost in Windows).

To connect to the already running `bash` service for using Annif commands, run

    docker exec -it -u $(id -u):$(id -g) annif_bash_1 bash

In the shell all the Annif commands can now be used, and the [Maui backend can be used as instructed](https://github.com/NatLibFi/Annif/wiki/Backend%3A-Maui#example-configuration-for-annif), _with the exception that  in the endpoint entries of `projects.cfg` file the `localhost` needs to be replaced with `mauiserver`_ (i.e. the full entry is then `endpoint=http://mauiserver:8080/mauiserver/`). 

The `docker-compose.yml` can be edited to remove unnecessary services, e.g. if if one only wants to use the Maui backend. Note that [the mauiserver container can also be run withouth `docker-compose`](https://github.com/NatLibFi/MauiServer/tree/master#usage-with-docker).

Note also that the `docker run` or `docker-compose up` commands do not automatically fetch a new version of an image, even if one is available in repository. To update to the most recent image or images, you must run [`docker pull IMAGE_NAME`](https://docs.docker.com/engine/reference/commandline/pull/) or [`docker-compose pull`](https://docs.docker.com/compose/reference/pull/).

# Using Docker in Annif development

It is possible to mount also the Annif source code into the container, which allows editing the code in the host system while running Annif and tests in the container. For this an image that includes the tests needs to be build:

    docker build -f Dockerfile-dev -t annif-dev .

Then a container from that image can be run:

    docker run \
        -v ~/annif-projects:/annif-projects \
        -v $(pwd):/Annif \
        -u $(id -u):$(id -g) \
        -it annif-dev bash


# Steps for creating data image and data deployment in Portainer

1. Train models and store `projects.cfg` and `data/` directory to `~/annif-projects`.

2. Build a data image (which [could also be versioned with a custom tag](https://docs.docker.com/engine/reference/commandline/build/#tag-an-image--t), the default tag is `latest`):

    ```docker build -t quay.io/natlibfi/annif-data -f Dockerfile-data ~/annif-projects```

    Here the data for models are included in the image, but the corpora are not (even if they happen to reside in `~/annif-projects`).

3. Push the image to https://quay.io/repository/natlibfi/annif-data repository: 

    ```docker push quay.io/natlibfi/annif-data```

4. In the [Services view of Portainer](https://portainer.kansalliskirjasto.fi/#/services) first select the data service (`annif-test_data`) and update it using the GUI button. Select to pull the latest image version when asked. Then, to make Annif use the new data, similarly update the `annif-test_gunicorn_server` service (now pulling the latest image is not necessary).

<!--- # In [docker-compose-portainer.yml](https://github.com/NatLibFi/Annif/blob/master/docker-compose-portainer.yml#L38) the `/annif-projects` directory of this data image is mounted to the data volume and the directory is then accessible by other services defined in the compose file.
--->

