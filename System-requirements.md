# OS environment

Annif is being developed on a mix of Ubuntu 16.04 and 18.04 machines. However, it should work on any Linux distribution with some work. Annif itself only requires a Python 3.5 (or newer) environment, but some of the optional extensions require native code libraries which may be challenging to install in some environments. 

# Resource requirements

* At least 8GB of RAM; 16GB or more is recommended if you are planning on hosting several models, e.g. different vocabularies and/or languages.
* Annif itself requires very little disk space, but corpora and models can be large, thus 100GB disk space is recommended
* Multiple CPU cores are useful, e.g. for building fastText models which can take advantage of parallel processing
