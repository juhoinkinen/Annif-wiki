# Install Docker

To be able to use Docker in your system, you need to have installed Docker-engine. Step-by-step instructions for this for Windows, Mac, and Linux distributions can be found in [Docker documentation](https://docs.docker.com/install/). 

# Running Annif in Docker container

In case you are using Linux, you can start bash shell in a container created from the Annif image with:

    docker run -it annif bash 

If this is the first time you are using the Annif image, the image will now be downloaded from [Docker Hub](https://hub.docker.com/). A bash shell starts in the container, where it is possible to run Annif [[Commands]] (here the `-it` flag is for enabling interactive mode).

However, the Annif image itself does not contain any vocabulary or training data. A directory containing these can be bind mounted as a [volume](https://docs.docker.com/storage/volumes/) from the host file system to the container using the syntax `-v /absolute_path/on/host:/path/in/container` after the `docker run` command<sup id="a1">[1](#myfootnote1)</sup>. To bind mount the current working directory on host, `$(pwd)` can be used instead explicitly writing the absolute path. Also, the user in a docker container is by default not the same as on the host system and any file created in a container is not owned by the host user, and with bind-mounts this can lead to issues with file permissions. Therefore it is best to make [the user in the container](https://docs.docker.com/engine/reference/run/#user) the same as on the host, using `-u $(id -u):$(id -g)`. With these two flags the command to run bash in a container with Annif looks like this:

    docker run \
        -v $(pwd):/annif_projects \
        -u $(id -u):$(id -g) \
        -it annif bash

Now the directory where the command was issued on host is mounted with name `annif_projects` on the root of the container filesystem. 

The Annif projects configuration file `projects.cnf` needs to be in the directory where Annif commands are run. There is a draft configuration file coming with the Annif image, which can be copied to the `/annif_projects` directory:

    cp /Annif/projects.cfg.dist  /annif_projects/projects.cfg


The vocabulary and training data (e.g. [Annif-corpora](https://github.com/NatLibFi/Annif-corpora)) can be fetched using the host system to the mounted directory.

Now everything is set up for running Annif in the container!


<a name="myfootnote1">1</a>:
Alternatively, it is possible to create and bind a [named volume](https://success.docker.com/article/different-types-of-volumes), which initially is empty, and copy or fetch data into it from container, e.g. using wget from [Annif-corpora Git Hub page](https://github.com/NatLibFi/Annif-corpora):

`wget -O - https://github.com/NatLibFi/Annif-corpora/tarball/master | tar xz`


