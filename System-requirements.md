# Supported Operating Systems

- **Linux:** Annif is being developed on Ubuntu machines. However, it should work on any Linux distribution with some work.
- **Other OS:** Annif itself runs anywhere Python 3.9+ is available. Some optional features may require native libraries that are harder to install on non-Linux systems.
- **OS with Docker:** Annif is available as a pre-built [Docker image](https://quay.io/repository/natlibfi/annif), which can be used on any OS supporting Docker. See the [[Usage-with-Docker]] page for details.

# Hardware Recommendations

- **Memory:** Minimum 8GB RAM. For hosting multiple models, e.g. for different vocabularies and/or languages, 16GB+ is recommended.
- **Disk Space:** Annif itself requires up to 2GB disk space with all optional features installed. Corpora for training and the trained models can be large, thus 100GB disk space is recommended.
- **CPU:** Multiple CPU cores (ideally 4+) are useful, e.g. for building fastText and Omikuji models which can take advantage of parallel processing.

---

[[← Getting started|Getting-started]] | [[Corpus formats →|Corpus-formats]]