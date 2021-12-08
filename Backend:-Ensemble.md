The `ensemble` backend implements a simple ensemble, i.e. a method for combining results from multiple backends. It has to be configured with source projects. Requests for subject suggestions will be re-routed to the source projects, then combined by calculating the mean of scores returned by each source backend for each concept.

It is easy to start using because it requires no algorithm-specific configuration apart from the `sources` setting.

## Example configuration

```
[ensemble-en]
name=Ensemble English
language=en
backend=ensemble
sources=tfidf-en,mllm-en
limit=100
vocab=yso-en
```

The `sources` setting is a comma-separated list of projects whose results will be combined. Optional weights may be given like this:

    sources=tfidf-en:1,mllm-en:2

This setting would give twice as much weight on results from `mllm-en` compared to results from `tfidf-en`.

## Usage

Load a vocabulary:

    annif loadvoc ensemble-en /path/to/Annif-corpora/vocab/yso-en.tsv

Training is not possible. There is nothing to train in this simple ensemble.

Test the model with a single document:

    cat document.txt | annif suggest ensemble-en

Evaluate a directory full of files in fulltext [[document corpus|Document corpus formats]] format:

    annif eval ensemble-en /path/to/documents/