# OS environment

Annif is being developed on a mix of Ubuntu 18.04 and 20.04 machines. However, it should work on any Linux distribution with some work. As for other OS:s, Annif itself is quite portable as it only requires a Python 3.6 (or newer) environment, although some of the optional extensions require native code libraries which may be challenging to install in some environments. 

Annif is also available as a pre-built [Docker image](https://quay.io/repository/natlibfi/annif) that can be run on all operating systems supporting Docker; see the [Usage-with-Docker wiki page](https://github.com/NatLibFi/Annif/wiki/Usage-with-Docker).

# Resource requirements

* At least 8GB of RAM; 16GB or more is recommended if you are planning on hosting several models, e.g. different vocabularies and/or languages.
* Annif itself requires very little disk space, but corpora and models can be large, thus 100GB disk space is recommended
* Multiple CPU cores are useful, e.g. for building fastText models which can take advantage of parallel processing
