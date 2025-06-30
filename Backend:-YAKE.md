The `yake` backend is a wrapper around [YAKE library](https://github.com/LIAAD/yake), which performs unsupervised automatic keyword extraction.

As YAKE is based on an unsupervised algorithm, the projects using it do not require training.

The keywords found by YAKE are searched from an index, which is formed from the SKOS vocabulary labels. The index can include `prefLabels`, `altLabels` and/or `hiddenLabels`. Keywords and labels in the index are lemmatized and sorted alphabetically for matching.

The YAKE backend is based on lexical principle, but currently it does not perform as well as the other lexical backends (MLLM or STWFSA, which are from the beginning designed to utilize the SKOS vocabulary features). However, the (free) keyword extraction operation offers a possibility to add new features to Annif, especially the feature for suggesting new terms for a vocabulary (the keywords not found in the vocabulary).
Currently the keywords not found from the vocabulary are shown in the debug log. However, at the moment setting the log-level for server mode (command `annif run`) is not possible.

## Installation

> [!NOTE]
> Please note that the [YAKE](https://github.com/LIAAD/yake) library is licended
> under [GPLv3](https://www.gnu.org/licenses/gpl-3.0.txt), while Annif is
> licensed under the Apache License 2.0. The licenses are compatible, but
> depending on legal interpretation, the terms of the GPLv3 (for example the
> requirement to publish corresponding source code when publishing an executable
> application) may be considered to apply to the whole of Annif+Yake if you
> decide to install the optional YAKE dependency.

For installation see [Optional features and dependencies](https://github.com/NatLibFi/Annif/wiki/Optional-features-and-dependencies).

## Example configuration

A minimal configuration that relies on default values:

```
[yso-yake-en]
language=en
backend=yake
analyzer=snowball(english)
vocab=yso
```

For long texts it can be advantageous to use the `limit` transformation [project setting](https://github.com/NatLibFi/Annif/wiki/Project-configuration) to truncate the documents before passing them to YAKE. For Finnish thesis and dissertations good results can be achieved with `transform=limit(20000)`.

### Backend-specific parameters

The `label_types` and `remove_parentheses` parameters are used for constructing the label index.
Note that if these parameters are changed after the label index has been created, which occurs on the first `suggest` call for a project, the update does not change the index, but the project then needs to be reset by `annif clear <project>`.
Resetting is needed also after vocabulary update.

The other parameters are passed to YAKE when extracting keywords; for the detailed description of them and the YAKE algorithm see the [article by R. Campos et al.](https://www.sciencedirect.com/science/article/abs/pii/S0020025519308588?via%3Dihub).

Parameter |  Description
-------- | --------------------------------------------------
label_types | SKOS label types to use in matching. Values are given in a comma separated list of `prefLabel`, `altLabel`, and/or `hiddenLabel`. Defaults to `prefLabel, altLabel`.
remove_parentheses | Whether to remove parts of SKOS labels inside parentheses (a specifier for a label, e.g. `(photography)` in `films (photography)`). Value needs to be interpretable as a boolean, e.g. `True/False`. Defaults to `False`.
window_size | Distance (in number of tokens) considered when computing co-occurances of tokens. Defaults to 1.
max_ngram_size | Maximum number of consequtive words to use in forming candidate keywords. Defaults to 4.
deduplication_algo | Algorithm to measure the similarity of candidate keywords for deduplication: `levs`, `jaro` or `seqm`. Defaults to `levs`.
deduplication_threshold | Threshold for the value of the similarity measure for deduplication. Defaults to 0.9.
num_keywords | Limit for the number of keywords that YAKE extracts. Defaults to 100.

## Usage

Load a vocabulary:

    annif load-vocab yso /path/to/Annif-corpora/vocab/yso-skos.ttl

Training is not necessary or possible. Test the model with a single document:

    cat document.txt | annif suggest yso-yake-en

Evaluate a directory full of files in fulltext [[document corpus|Document corpus formats]] format:

    annif eval yso-yake-en /path/to/documents/

---
[[← STWFSA|Backend:-STWFSA]] | [[SVC →|Backend:-SVC]]
