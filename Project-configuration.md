Annif projects are used to set up backends and configure them with a specific vocabulary and parameters. Projects are defined in a file commonly called `projects.cfg`. By default, Annif looks for this file in the current directory where it is executed, but you can specify another path using the `ANNIF_PROJECTS` environment variable or the `--projects`  option after a command. 

Here is an example project configuration. The configuration file format follows the [INI style](https://en.wikipedia.org/wiki/INI_file) and each project is represented as a section, with the identifier of the project as the section name.

```
[tfidf-en]
name=TF-IDF English
language=en
backend=tfidf
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
| backend   | The backend (algorithm) that the project uses. See below for details. |
| analyzer   | The analyzer used to pre-process and tokenize text. See below for details. |
| limit      | The maximum number of results (subjects/concepts) to return |
| vocab      | An identifier for the vocabulary used by this project |

Some backends also require additional parameters (`tfidf` doesn't).

For some commands it is possible to override a parameter set in the configuration file using the `--backend-param` option; the  syntax for this is `--backend-param <backend>.<parameter>=<value>`.

# Backends

For a list of supported backends, see [[Home]].

The [[ensemble|Backend: Ensemble]] backend provides a way of combining results from multiple backends.
 
# Analyzers

Analyzers are used to pre-process text. For a list of supported analyzers, see [[Analyzers]].

# Vocabularies

Most backends require a subject vocabulary. A vocabulary itself doesn't need any configuration, but the project must define an identifier for the vocabulary it uses. All projects with the same vocabulary identifier will share the same vocabulary, so the vocabulary only needs to be loaded (using the `loadvoc` command) for one of them.

Note that in Annif, vocabularies, like projects, are monolingual. If you load a multilingual SKOS vocabulary for a project, only the labels in the language defined for the project will be loaded.