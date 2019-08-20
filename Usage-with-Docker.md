# Install Docker

To be able to use Docker in your system, you need to have installed Docker-engine. Step-by-step instructions for this for Windows, Mac, and Linux distributions can be found in [Docker documentation](https://docs.docker.com/install/). 

# Running Annif in Docker container

In case you are using Linux, you can get the Annif docker image from the docker registry at https://quay.io/ with:

    docker pull quay.io/natlibfi/annif

Then the bash shell can be started in a container with ready-to-use Annif with:

    docker run -it quay.io/natlibfi/annif bash 

In the shell it is possible to run Annif [[Commands]] (here the `-it` flag is for enabling interactive mode). The container can be exited with `exit`.

However, the Annif image itself does not contain any vocabulary or training data. A directory containing these can be bind mounted as a [volume](https://docs.docker.com/storage/volumes/) from the host file system to the container using the syntax `-v /absolute_path/on/host:/path/in/container` after the `docker run` command<sup id="a1">[1](#myfootnote1)</sup>. Also, the user in a docker container is by default not the same as on the host system and any file created in a container is not owned by the host user, and with bind-mounts this can lead to issues with file permissions. Therefore it is best to make [the user in the container](https://docs.docker.com/engine/reference/run/#user) the same as on the host, using `-u $(id -u):$(id -g)`. With these flags the command to run bash in a container with Annif looks like this:

    docker run \
        -v ~/annif-projects:/annif-projects \
        -u $(id -u):$(id -g) \
        -it quay.io/natlibfi/annif bash

Here the `annif-projects/` directory is assumed to exist in home directory on host (and it is mounted with the same name on the root of the container filesystem). From here on the post-installation steps for using Annif in [[Getting Started]] can be followed. 

Specifically, the template configuration file [`projects.cfg.dist`](https://github.com/NatLibFi/Annif/blob/master/projects.cfg.dist) can be placed to `~/annif-projects/` in the host system with the name `projects.cfg` along the vocabulary and training data (e.g. [Annif-corpora](https://github.com/NatLibFi/Annif-corpora)).

*Note that any data should not be stored in other locations in the container but in the mounted directory*, as after the container has stopped, [it is not convenient to gain access to the data again](https://docs.docker.com/engine/reference/commandline/commit/).

If the web UI started by `annif run` is used from within the container, also the flag `--network="host"` [needs to be included in the `docker run` command](https://docs.docker.com/engine/reference/run/#network-host).


# Using Annif with Gunicorn, NGINX, and Maui backend
Different containerized services can be conveniently linked together by using [docker-compose](https://docs.docker.com/compose/). The instructions to set up the services are in [`docker-compose.yml`](https://github.com/NatLibFi/Annif/blob/issue278-dockerize-annif/docker-compose.yml), which in this case instructs docker to start separate containers for 
* bash shell to run  Annif commands
* Gunicorn server running Annif
* NGINX proxy server
* [Mauiservice](https://github.com/NatLibFi/mauiservice/tree/dockerize-mauiservice) to access [Maui backend](https://github.com/NatLibFi/Annif/wiki/Backend%3A-Maui)

To start these services, while in `Annif/` run 

    ANNIF_PROJECTS=~/annif-projects UID=${UID} GID=${GID} docker-compose up

Here the environment variables are needed for mounting the directory for vocabulary and training data files and setting the user in the container the same as on the host. Once the services have started, the Annif web UI is accessible at http://localhost/ run by NGINX.

To connect to the already running `bash` service for using Annif commands, run

    docker exec -it -u $(id -u):$(id -g) annif_bash_1 bash

To create model for Maui backend (see [[here | backend:-maui#Creating a model for Maui ]] for details), run

    docker exec -u $(id -u):$(id -g) annif_mauiservice_1 \
        java -Xmx4G -cp maui-1.4.5-jar-with-dependencies.jar com.entopix.maui.main.MauiModelBuilder -l /annif-projects/Annif-corpora/fulltext/kirjastonhoitaja/maui-train/ -m /annif-projects/kirjastonhoitaja -v /annif-projects/Annif-corpora/vocab/yso-skos.rdf -f skos -i fi -s StopwordsFinnish -t CachingFinnishStemmer

To connect to the Maui backend while running via `docker-compose`, in the endpoint entries of `projects.cfg` file the default `localhost` needs to be replaced by `mauiservice` (and when trained as above the model name is `kirjastonhoitaja`, and the full entry is then `endpoint=http://mauiservice:8080/mauiservice/kirjastonhoitaja/analyze`). Note also that to be able to use a new model the services need to be restarted.

A custom Mauiservice configuration file can be used by changing the path in the env `JAVA_OPTS="-DMAUISERVICE_CONFIGURATION=/srv/maui/mauiservice.ini"` in [`docker-compose.yml`](https://github.com/NatLibFi/Annif/blob/master/docker-compose.yml#L21) to the path of the customized file, e.g. to `/annif-projects/mauiservice.ini`, which is then mounted from the host system and can be conveniently edited.

The `docker-compose.yml` can be edited to remove unnecessary services, e.g. if if one only wants to use the Maui backend. Note that [the mauiservice container can also be run withouth `docker-compose`](https://github.com/NatLibFi/mauiservice/blob/dockerize-mauiservice/DEVELOPER.md#usage-with-docker), and in that case the container needs to be started with `--network="host"` flag so it is accessible from the host system.


# Using Docker in Annif development

It is possible to mount also the Annif source code into the container, which allows editing the code in the host system while running Annif and tests (included in the `annif-dev` image) in the container:

    docker run \
        -v ~/annif-projects:/annif-projects \
        -v $(pwd):/Annif \
        -u $(id -u):$(id -g) \
        -it quay.io/natlibfi/annif:dev bash

Here it is assumed that the current working directory is the one containing the source code (thus the use of `$(pwd)`).


# Steps for creating data image for Portainer

1. Train models and store `projects.cfg` and `data/` directory to `~/annif-projects`.

2. Build a data image (which [could also be versioned with a custom tag](https://docs.docker.com/engine/reference/commandline/build/#tag-an-image--t), the default tag is `latest`):

    ```docker build -t quay.io/natlibfi/annif-data -f Dockerfile-data ~/annif-projects```

    Here the data for models are included in the image, but the corpora are not (even if they happen to reside in `~/annif-projects`).

3. Push the image to https://quay.io/repository/natlibfi/annif-data: 

    ```docker push quay.io/natlibfi/annif-data```

In [docker-compose-portainer.yml](https://github.com/NatLibFi/Annif/blob/master/docker-compose-portainer.yml#L38) the `/annif-projects` directory of this data image is mounted to the data volume and the directory is then accessible by other services defined in the compose file.

&nbsp;
&nbsp;

<a name="myfootnote1">1</a>:
Alternatively, it is possible to create and bind a [named volume](https://success.docker.com/article/different-types-of-volumes), which initially is empty, and get data into it by [copying](https://docs.docker.com/engine/reference/commandline/cp/) from host or fetching from internet, e.g. using wget in a running container to dowload [Annif-corpora Git Hub page](https://github.com/NatLibFi/Annif-corpora):

`wget -O - https://github.com/NatLibFi/Annif-corpora/tarball/master | tar xz`


