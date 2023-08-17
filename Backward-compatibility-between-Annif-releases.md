Annif follows [semantic versioning](https://semver.org/) principles. The guiding SemVer principle is that if the public API is changed, a new major version should be released. 

In order to apply the SemVer principles to the case of Annif, it is necessary to define what is considered the public API of Annif. Annif is primarily a toolkit and its "API" consists of many parts, such as the command line interface, the REST API, the web user interface, Python code as well as configuration and data files. This document aims to define the policy for what may and may not change in different parts of the public API for **minor releases** (e.g. 1.0.0 -> 1.1.0).

For patch releases (e.g. 1.0.0 -> 1.0.1) that are meant only for bug fixes, backward compatibility requirements are obviously more strict; only the minimum amount of changes necessary to fix the bug(s) will be made to the Annif codebase and if there are any worries about introducing backward compatibility issues for users, a minor version is released instead of a patch release.

# CLI commands

Old CLI commands (including options) **must keep working, including commands that rely on default values.**

It is possible (and allowed) to _deprecate_ some CLI commands in a minor release. However, **removing a CLI command completely requires a major release**.

The **output of `eval` and `hyperopt` commands must stay the same** except that it's allowed to introduce new output lines that follow the same syntax (e.g. new evaluation metrics).

# REST API method calls

The REST API already includes a version number prefix (currently `/v1/`) so it is expected that **any backwards incompatible changes require incrementing that version prefix**. support for the old API version is removed, **a new major release of Annif must be made**. 

Defining a breaking change for the REST API is out of scope for the discussion on Annif version numbers. For identifying the difference of two OpenAPI specifications the [openapi-diff](https://github.com/OpenAPITools/openapi-diff) tool can be used.

# Web UI

**Any kind of changes to the Web UI may be made** in minor releases of Annif. This is because Web UI is not something that is normally used programmatically but by human users.

# Configuration files

Old configuration files (e.g. `projects.cfg`, `projects.toml`) files **must keep working and default values must stay the same.**

# Vocabulary and project/model data

We aim to retain the compatibility of vocabulary and model/project data for subsequent minor releases, but we cannot guarantee this, because Annif relies on several external libraries which have different policies for backwards compatibility. Thus, when there are good reasons **old vocabularies and projects may become incompatible** (thus requiring reloading/retraining). In these cases **the incompatibility must be clearly communicated in the release notes** and clear **error messages must be displayed** when attempting to use incompatible old models.

Any incompatibility **must not silently lead to erroneous suggestion results**.

# Python API

Annif is primarily a tool, not a Python library. Any changes to the Python API (e.g. method signatures) **are considered internal and are allowed** in minor version releases.

# Python environment

Annif **must support three consecutive Python versions**; it is allowed to drop support for an old Python version as long as any three versions are still supported.

The supported Python versions **must be expressed in the release notes**.

# Analyzers

Like in the case of vocabularies and projects, we aim to retain the compatibility when upgrading analyzer versions for minor releases, but for good reasons this **may lead to projects become incompatible** (thus require retraining). In these cases **the incompatibility must be clearly communicated in the release notes** and proper **error messages must be displayed** when attempting to use incompatible old models.

Any incompatibility **must not silently lead to erroneous suggestion results**.

# Docker images

The Python version used in the Docker image of Annif **is allowed to be updated** as long as existing models and vocabularies remain compatible.

The base image that we use is an official Python image from DockerHub, which are based on Debian. The operating system should not affect Annif functionality, so **updating the base image to a new OS version is allowed**, as long as existing models and vocabularies remain compatible.

The image of a given Annif version will be occasionally rebuilt to apply security updates to it. The vocabularies and models/projects **must remain compatible** between the rebuilds.
