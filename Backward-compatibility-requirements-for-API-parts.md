For following [semantic versioning](https://semver.org/) principles it is necessary to document what is considered the public API of Annif. The guiding SemVer principle is that if the public API is changed, a new major version should be released. 

This document aims to define the policy for what may and may not change in different parts of the public API for **minor releases** (e.g. 1.0.0 -> 1.1.0).

# CLI commands

**Old CLI commands (including options) must keep working, including commands that rely on default values.**

It is possible (and allowed) to _deprecate_ some CLI commands in a minor release. However, **removing a CLI command completely requires a major release**.

The output of CLI commands is also be considered part of the API, in particular for commands like `annif eval` whose output is meant to be easily processed using tools like `grep` and `sed`. The **output of /some/ commands (including `eval` and `hyperopt`) must stay the same** except that it's allowed to introduce new output lines that follow the same syntax (e.g. new evaluation metrics).

# REST API method calls

The REST API already includes a version number prefix (currently `/v1/`) so it is expected that **any backwards incompatible changes require incrementing that version prefix**. If support for the old API version is removed, this would naturally also trigger a new major release of Annif. What is considered a breaking change for the REST API is another discussion, but that is out of scope for Annif version numbers.

There exists [openapi-diff](https://github.com/OpenAPITools/openapi-diff) tool for automatically checking the difference of two OpenAPI specifications (3.x) and their compatibility.

# Web UI

The Web UI relies on the REST API for all operations. **Any kind of changes to the Web UI may be made** in minor releases of Annif. This is because Web UI is not something that is normally used programmatically but by human users.

# Configuration files

**Old configuration files (e.g. `projects.cfg`, `projects.toml`) files must keep working and default values must stay the same.**

# Vocabulary data

TBD, this received a different opinion by Moritz and Sandro.

> The vocabulary is loaded with the annif loadvoc command and ideally would not need to be reloaded due to a version upgrade. So the suggested > policy is that for minor Annif releases, previously loaded vocabularies should keep working. If there are changes to the format on disk, there should be an easy (and if possible, automatic) migration mechanism available so that a full reload is not necessary.

Moritz:
> If you have to break the compatibility you should make sure that old vocabularies can not be loaded.
I.e., no silent failures that would lead to strange prediction"

Sandro: 
> The fact that previously loaded vocabularies should continue to work with minor Annif versions is not a hard condition for versioning. It should also be acceptable for the user to reload the vocabulary when a version changes. In our experience, reloading the vocabulary is fast enough. It is a mandatory part of our command chain for training models."

# Project/model data

Annif relies a lot on external libraries for the backends, and some of those tend to change frequently in backwards-incompatible ways (e.g. Omikuji), or at least complain about data files saved using previous versions (e.g. scikit-learn vectorizers used by many backends). 

- ~Old projects must keep working without showing any deprecation warnings.~
- ~Old projects must keep working, but it's OK to show warnings.~
- Old projects for the core backends (installed by default, e.g. tfidf, svc, mllm, stwfsa) must keep working, but optional backends (e.g. fasttext, omikuji, nn_ensemble) may require retraining.
- Old projects may require retraining as long as this is clearly communicated to the user in the release notes and proper error messages are displayed when attempting to use incompatible old models.

# Python API

Annif isn't primarily a software library. Thus, **any changes to the Python API (e.g. method signatures) are considered internal and are allowed** in minor versions.

# Python environment

Annif has tried to maintain support for three consecutive Python versions and this window is occasionally shifted forward. Nowadays new Python minor releases (e.g. 3.9, 3.10, 3.11) are made on a 12-month schedule, with the release made in October. Would dropping support for an old version of Python require a major release of Annif? Possible policies for minor releases:

- ~Annif must keep supporting all previously supported Python versions.~
- ~Annif must keep supporting all previously supported Python versions, as long as they are supported by the upstream Python project; it is OK to drop support for EOL versions.~
- Annif must support three consecutive Python versions; it is OK to drop support for an old Python version as long as any three versions are still working.
- Annif may drop support for old Python versions.

The supported Python versions should be included in the release notes (and naturally changes to them).

The first (strictest) option would in the long term imply a new major version of Annif at least once per year, because old Python versions cannot be supported indefinitely. I think the second or third options make the most sense. The second option is possibly too conservative (e.g. support for Python 3.7 could be dropped in June 2023 and for Python 3.8 in October 2024 - but support for 3.7 was dropped in Annif 0.58 released in August 2022!), while the third option reflects the status quo.

# Analyzers

Analyzers are used to pre-process text and do things like stemming and lemmatization. But they also change over time, and a word may no longer be lemmatized the same way - see https://github.com/NatLibFi/Annif/issues/492#issuecomment-851419544 for an actual example. New versions of lemmatizers like spaCy and Simplemma are released quite frequently and we probably want to keep up with them without too much delay.

But there is a problem with old models trained using the old analyzers - they assume certain words are lemmatized in one way, and when that assumption breaks, the model may perform worse than it did before. This could be a particularly big issue for the MLLM lexical model, which relies a lot on words being lemmatized in a consistent way.

# Docker images

The Python version used in the Docker image of Annif **is allowed to be updated**.

The base image  has been an official Python image from DockerHub, which are based on Debian. Debian releases have been made every two years. However, the operating system should not affect Annif functionality, so **updating the base image to new OS version is allowed**.
