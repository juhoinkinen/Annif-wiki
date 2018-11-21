Annif projects are used to set up backends and configure them with a specific vocabulary and parameters. Projects are defined in a file commonly called `projects.cfg`. By default, Annif looks for this file in the current directory where it is executed, but you can specify another path using the `ANNIF_PROJECTS` environment variable.

Here is an example project configuration. The configuration file format follows the [INI style](https://en.wikipedia.org/wiki/INI_file) and each project is represented as a section, with the identifier of the project as the section name.

```
[tfidf-en]
name=TF-IDF English
language=en
backends=tfidf
analyzer=snowball(english)
limit=100
vocab=yso-en
```

A project has the following attributes:

| Setting    | Description |
| ---------- | ----------- |
| identifier | An identifier consisting of alphanumeric characters and basic punctuation. |
| name       | A human-friendly name. |
| language   | IETF [BCP 47](https://en.wikipedia.org/wiki/IETF_language_tag) language code. |
| backends   | The backend (algorithm) that the project uses. See below for details. |
| analyzer   | The analyzer used to pre-process and tokenize text. See below for details. |
| limit      | The maximum number of results (subjects/concepts) to return |
| vocab      | An identifier for the vocabulary used by this project |

# Backends for a project

In most cases the `backends` attribute will contain just a single backend, for example `tfidf` or `fasttext`. However, it is possible to define a comma-separated list of backends whose results will be combined, like this:

    tfidf,fasttext

Furthermore, you may give weights to each backend by suffixing the backend id with a colon and a weight value:

    tfidf:1,fasttext:2

This way the results will be combined using a weighted average. In the above case the results from the `fasttext` backend will be weighted so that they are twice as important as the ones from `tfidf`. When not specified, the default weights for a backend is 1.

The [[Backend: Ensemble|ensemble]] backend provides a more general way of combining results from multiple backends.
 
# Analyzers for a project

TBD

# Vocabularies

TBD