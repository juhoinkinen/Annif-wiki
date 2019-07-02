The `vw_ensemble` backend implements a trainable dynamic ensemble that intelligently combines results from multiple projects. Subject suggestion requests to the ensemble backend will be re-routed to the source projects. The results from the source projects will be re-weighted using a [Vowpal Wabbit](https://github.com/VowpalWabbit/vowpal_wabbit) machine learning system.

This backend is similar in spirit to the PAV backend, but unlike PAV, this backend also supports [online learning](https://en.wikipedia.org/wiki/Online_machine_learning), which means that it can be further trained during use via the `learn` command in the Annif CLI and REST API.

## Installation

See [[Optional features and dependencies]]

## Example configuration

```
[vw-ensemble-en]
name=VW ensemble English
language=en
backends=vw_ensemble
sources=tfidf-en,maui-en
limit=100
vocab=yso-en
bit_precision=20
passes=4
decay_rate=0.005
```

### Backend-specific parameters

With the exception of `sources`, `limit` and `decay_rate`, the parameters are passed directly to the selected VW algorithm. If you omit a parameter, a default value is used. 

Parameter | Description
--------- | --------------------------------------------------
sources | List of source projects from which to collect suggestions
limit | Maximum number of results to return
decay_rate | Adjusts how quickly the model will start making adjustments to suggestion scores
bit_precision | Determines size of feature space. VW default is rather low (18), usually 20-25 are better.
learning_rate | Learning rate
loss_function | Function used when training model. Valid values: `squared`, `logistic`, `hinge`. Default is `squared` and this seems to work best.
l1 | L1 regularization lambda value (usually a small float value)
l2 | L2 regularization lambda value (usually a small float value)
passes | How many passes over training data to perform

You can check out the [VW documentation about command line arguments](https://github.com/VowpalWabbit/vowpal_wabbit/wiki/Command-line-arguments) for more details about the parameters.

The `sources` setting is a comma-separated list of projects whose results will be combined. Optional weights may be given like this:

    sources=tfidf-en:1,maui-en:2

This setting would give twice as much weight on results from `maui-en` compared to results from `tfidf-en`.

The `decay_rate` setting adjusts how eagerly the VW model will adjust the suggestion scores coming from the source projects, as a function of the number of training examples available for a particular concept. A low value (e.g. `0.001`) means that only small adjustments are made, while larger values such as `0.1` mean that the model quickly starts adapting even with a small number of training examples.

## Usage

Load a vocabulary:

    annif loadvoc vw-ensemble-en /path/to/Annif-corpora/vocab/yso-en.tsv

Train the ensemble:

    annif train vw-ensemble-en /path/to/Annif-corpora/training/yso-finna-en.tsv.gz

Test the model with a single document:

    cat document.txt | annif suggest vw-ensemble-en

Learn from additional documents:

    annif learn vw-ensemble-en /path/to/documents.tsv


Evaluate a directory full of files in fulltext [[document corpus|Document corpus formats]] format:

    annif eval vw-ensemble-en /path/to/documents/