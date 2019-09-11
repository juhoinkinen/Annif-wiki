For troubleshooting and discussion, check out the Annif-users -mailing list/forum. Also feel free to open GitHub-issues if need be.

# General

- To have more information on what might be wrong, it is possible see debug messages by adding `-v DEBUG` flag to a command. For example: 
    ```
    $ cat document.txt | annif suggest tfidf-en -v DEBUG
    debug: creating app with configuration annif.default_config.Config
    debug: Suggesting subjects for text "Arkeologiaa sanotaan..." (len=307)
    debug: Backend tfidf: loading similarity index from data/projects/tfidf-en/tfidf-index
    debug: Backend tfidf: Suggesting subjects for text "Arkeologiaa sanotaan..." (len=307)
    debug: loading vectorizer from data/projects/tfidf-en/vectorizer
    debug: loading subjects from data/vocabs/yso-en/subjects
    debug: Got 100 hits from backend tfidf
    debug: 100 hits from backend
    <http://www.yso.fi/onto/yso/p17588>	tai chi	0.3293267447549477
    <http://www.yso.fi/onto/yso/p6298>	water gymnastics	0.06801441885961519
    <http://www.yso.fi/onto/yso/p6854>	martial arts	0.06750483448477115
    <http://www.yso.fi/onto/yso/p3914>	ethnohistory	0.061006836768372916
    <http://www.yso.fi/onto/yso/p18697>	international matches	0.052560500151367734
    <http://www.yso.fi/onto/yso/p19591>	jujutsu	0.05164832265188628
    <http://www.yso.fi/onto/yso/p17805>	taekwondo	0.04821890373154558
    <http://www.yso.fi/onto/yso/p22635>	killer whale	0.04158137531400643
    <http://www.yso.fi/onto/yso/p8059>	museum roads	0.04156596842455262
    <http://www.yso.fi/onto/yso/p12968>	pediatricians	0.0413787581489993
    ```

- Note that `annif loadvoc PROJECT_ID SUBJECTFILE` command loads the vocabulary defined for the project with `PROJECT_ID` (and overrides existing one), and also for other projects that use same vocabulary

# Docker Troubles

- Note that simple `docker run` or `docker-compose up` do not fetch a new version of an image, even if that existed in in a repository, but to use a more recent image than exists locally, you must do [`docker pull IMAGE_NAME`](https://docs.docker.com/engine/reference/commandline/pull/) or [`docker-compose pull`](https://docs.docker.com/compose/reference/pull/).

- When using Docker with Windows, the id-variables id -u and id -g will not work. Under Windows, Docker usually runs in its own virtual machine, and this virtual machine in turn is run by whoever installed it (and the permissions are set accordingly). You can try leaving the part with id-variables out when you've figured out how the permissions work in your setup...

- "[A version prior to Windows 10 18.09, published ports on Windows containers have an issue with loopback to the localhost. You can only reach container endpoints from the host using the container’s IP and port.](https://docs.docker.com/docker-for-windows/troubleshoot/#limitations-of-windows-containers-for-localhost-and-published-ports)"
