# Annif architecture

## Basic concepts

An installation of Annif may contain multiple independent **projects**, each
of which specifies a set of settings (e.g. backend, language and indexing
vocabulary). Projects are limited to a single language; multilingual
indexing can be performed by defining multiple projects, one per language.
See [[Project configuration]] for more details.

Each project defines a (typically large) number of **subjects**, which
reflect concepts from an indexing vocabulary. Subjects are typically created
from a corpus extracted from existing metadata records and/or indexed
documents. See [[Subject vocabulary formats]] for more information about the
supported subject formats.

Further, there can be several independent **backends** that provide subject suggestions. Backends can be integrated into Annif itself, or external services queried via APIs. A project can make use of several backends and combine their analysis results.

## Software architecture

[[/images/annif-architecture.png|Architecture diagram]]

Annif has a core application (using Flask and Connexion), which provides both a REST API (when run as a web application) and a command line interface. The command line interface supports administrative functionalities such as corpus management, quality evaluation and other auxiliary functions.