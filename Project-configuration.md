Annif projects are used to set up backends and configure them with a specific vocabulary and parameters. By default project settings are looked in (in this order)
1. `projects.cfg` file (in [INI format](https://en.wikipedia.org/wiki/INI_file))
2. `projects.toml` file (in [TOML format](https://en.wikipedia.org/wiki/TOML))
3. `projects.d` directory (containing `*.cfg` and/or `*.toml` files).

These configuration files and the directory are searched in the current working directory by default, but you can specify another path using the `ANNIF_PROJECTS` environment variable or the `--projects`  option after a command.

In the case of a configuration directory all the configuration files in that directory (filenames matching the patterns `*.cfg` or `*.toml`) are read and their contents catenated; this can ease the deployment of independently developed projects. The files are read in alphanumeric order, so in project listings the projects defined `0-projects.toml` appear before the projects defined in`1-projects.cfg`, for example.

Below is an example project configuration in INI format. Each project is represented as a section, with the identifier of the project as the section name. Note that the TOML format differs essentially from INI format by requiring string values to be quoted.

```
[tfidf-en]
name=TF-IDF English
language=en
backend=tfidf
analyzer=snowball(english)
transform=pass
limit=100
vocab=yso
```

A project has the following attributes:

| Setting    | Description |
| ---------- | ----------- |
| identifier | An identifier consisting of alphanumeric characters and basic punctuation. |
| name       | A human-friendly name. |
| language   | IETF [BCP 47](https://en.wikipedia.org/wiki/IETF_language_tag) language code. |
| backend    | The backend (algorithm) that the project uses. See below for details. |
| analyzer   | The analyzer used to pre-process and tokenize text. See below for details. |
| limit      | The maximum number of results (subjects/concepts) to return. |
| transform  | The transformation to apply to text, by default no transformation is applied. See below for details. |
| vocab      | An identifier for the vocabulary used by this project |
| access     | Access level when project is accessed via the REST API. Can be `public` (default), `hidden` or `private`, see below.

Some backends also use additional parameters (`tfidf` doesn't), but if they are not present in the configuration file, default values are used.

For some commands it is possible to override a parameter set in the configuration file using the `--backend-param` option; the  syntax for this is `--backend-param <backend>.<parameter>=<value>`.

# Backends

For an overview of available backends, see [[Backends]].

# Analyzers

Analyzers are used by backends to pre-process text. For a list of supported analyzers, see [[Analyzers]].

# Transforms

Before text pre-processing by an analyzer the text can modified by applying a transformation to it, see [[Transforms]].

# Vocabularies

Most backends require a subject vocabulary. A vocabulary itself doesn't need any configuration, but the project must define an identifier for the vocabulary it uses. All projects with the same vocabulary identifier will share the same vocabulary, so the vocabulary only needs to be loaded (using the `load-vocab` command) for one of them.

A vocabulary can be multilingual, provided that it is loaded from a
multilingual SKOS file. The language of the labels of subject suggestions
is defined by the project's `language` setting, or it can be overridden in a
project by giving the language code in parentheses after the vocabulary id
(e.g. `vocab=lcsh(en)` in a Finnish language project).

# Access levels

Access levels can be used to restrict which projects are available via the REST API. The default access level is `public`, meaning that the project is visible in the response for the `projects` method and can be used for suggesting concepts. The `hidden` level makes the project invisible, but it can still be accessed if the project ID is known. The `private` level means that the project is not available at all via the REST API.