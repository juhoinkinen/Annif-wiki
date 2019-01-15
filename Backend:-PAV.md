The PAV backend implements a trainable dynamic ensemble that intelligently combines results from multiple projects. Analysis requests to the ensemble backend will be re-routed to the source projects. The results from the source projects will be re-weighted using [isotonic regression](https://en.wikipedia.org/wiki/Isotonic_regression), which attempts to convert raw scores to probabilities. The regression is implemented using the PAV algorithm [available in the scikit-learn library](https://scikit-learn.org/stable/modules/isotonic.html). The regression is performed separately for each concept and the results are combined by calculating the mean of regressed scores (i.e. estimated probabilities) for each concept.

## Example configuration

```
[pav-en]
name=PAV ensemble English
language=en
backends=ensemble
sources=tfidf-en,maui-en
min-docs=3
limit=100
vocab=yso-en
```

The `sources` setting is a comma-separated list of projects whose results will be combined. Optional weights may be given like this:

    sources=tfidf-en:1,maui-en:2

This setting would give twice as much weight on results from `maui-en` compared to results from `tfidf-en`.

The `min-docs` setting specifies how many positive examples of a concept are required in the training data in order to create a regression model for that concept. Recommended values are between 3 and 10. When not enough positive examples are available, raw scores are used instead, similar to the basic [[ensemble backend|Backend: Ensemble]].

## Usage

Load a vocabulary:

    annif loadvoc pav-en /path/to/Annif-corpora/vocab/yso-en.tsv

Train


Test the model with a single document:

    cat document.txt | annif analyze ensemble-en

Evaluate a directory full of files in fulltext [[document corpus|Document corpus formats]] format:

    annif eval ensemble-en /path/to/documents/