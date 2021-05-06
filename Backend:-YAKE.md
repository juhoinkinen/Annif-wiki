The `yake` backend is a wrapper around [YAKE library](https://github.com/LIAAD/yake), which performs unsupervised automatic keyword extraction. 

In the Annif backend the keywords found by YAKE are searched from an index, which is formed from the SKOS vocabulary labels. The index can include `prefLabels`, `altLabels` and/or `hiddenLabels`. Keywords and labels in the index are lemmatized and sorted alphabetically for mathing. 

The YAKE backend performs lexical matching, but does not perform as well as the other lexical backends (MLLM, STWFSA or Maui) as measured by evaluation results. However, the (free) keyword extraction operation offers a possibility to add new features in Annif, especially the feature for suggesting new terms for a vocabulary (the keywords not found in the vocabulary). Also the unsupervised approach can be useful in some cases: there is no need for training data.

## Installation

Note that the YAKE library is licensed under GPLv3, and installing YAKE library for this optional backend may result in GPLv3 terms to cover the application. 

For installation see [Optional features and dependencies](https://github.com/NatLibFi/Annif/wiki/Optional-features-and-dependencies).

## Example configuration

A minimal configuration that relies on default values:

```
[yso-yake-en]
language=en
backend=yake
analyzer=snowball(english)
vocab=yso-en
```

### Backend-specific parameters

The parameters of the first two lines used in the backend in constructing the label index and matching keywords to it, the parameters in the following lines are passed to YAKE when extracting keywords (see [description](https://github.com/LIAAD/yake#usage-command-line)):

Parameter |  Description
-------- | --------------------------------------------------
label_types | SKOS label types to use in matching. Values are given in a comma separated list of `prefLabel`, `altLabel`, and/or `hiddenLabel`. Defaults to `prefLabel, altLabel`.
remove_parentheses | Whether to remove parts of labels inside parentheses (a specifier in a label, e.g. `(photography)` in `films (photography)`). Value needs to be interpretable as a boolean, e.g. `True/False`. Defaults to `False`.
max_ngram_size | 4,
deduplication_threshold | 0.9
deduplication_algo | `levs`
window_size | 1
num_keywords | 100
features | None

## Usage

Load a vocabulary:

    annif loadvoc yso-yake-en /path/to/Annif-corpora/vocab/yso-skos.ttl

Training is not necessary or possible. Test the model with a single document:

    cat document.txt | annif suggest yso-yake-en

Evaluate a directory full of files in fulltext [[document corpus|Document corpus formats]] format:

    annif eval yso-yake-en /path/to/documents/