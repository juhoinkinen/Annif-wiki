The `omikuji` backend is a wrapper around [Omikuji](https://github.com/tomtung/omikuji), an efficient implementation of a family of tree-based machine learning algorithms. It can emulate Parabel and Bonsai, two state-of-the-art algorithms for extreme multilabel classification.

The quality of results has generally been extremely good, even without tuning of hyperparameters. Training can be computationally intensive; by default it will use all available CPU cores in parallel during the training phase. Also large amounts of RAM (several GB) may be required during training, but during use the memory usage is lower.

## Installation

See [[Optional features and dependencies]]

## Example configuration

This is a configuration that emulates Parabel. All the hyperparameters are left at their default values.

```
[omikuji-parabel-en]
name=Omikuji Parabel English
language=en
backend=omikuji
analyzer=snowball(english)
vocab=yso
```

This is a configuration that emulates Bonsai:

```
[omikuji-bonsai-en]
name=Omikuji Bonsai English
language=en
backend=omikuji
analyzer=snowball(english)
vocab=yso
cluster_balanced=False
cluster_k=100
max_depth=3
```

This is a configuration that performs AttentionXML-style collapsing of layers:

```
[omikuji-attention-en]
name=Omikuji Attention English
language=en
backend=omikuji
analyzer=snowball(english)
vocab=yso
cluster_balanced=False
cluster_k=2
collapse_every_n_layers=5
```

### Backend-specific parameters

The parameters are:

Parameter |  Description
-------- | --------------------------------------------------
limit | Maximum number of results to return
min_df | How many documents a word must appear in to be considered. Default: 1
ngram | Maximum length of word n-grams. Default: 1
cluster_balanced | Perform balanced k-means clustering instead of regular k-means clustering. Default: True
cluster_k | Number of clusters. Default: 2
max_depth | Maximum tree depth. Default: 20
collapse_every_n_layers | Number of adjacent layers to collapse. Default: 0 (disabled)

The `min_df` parameter controls the features (words/tokens) used to build the model. With the default setting of 1, all the words in the training set will be used, even ones that appear in only one training document. With a higher value such as 5, only those that appear in at least that many documents are included. Increasing the `min_df` value will decrease the size and training time of the model.

Setting the `ngram` parameter to 2 the vectorizer will use 2-grams as well 1-grams. This may improve the results of the model, but the model will be much larger. When using ngram>1, it probably makes sense to set `min_df` to something more than 1, otherwise there may be a huge number of pretty useless features.

Not all hyperparameters supported by Omikuji are currently implemented by this backend, only the ones necessary to emulate Parabel, Bonsai and the AttentionXML-like layer collapsing mode. See the [omikuji README](https://github.com/tomtung/omikuji/blob/master/README.md) for details about the hyperparameters.

## Retraining with cached training data

Preprocessing the training data can take a significant portion of the training time. If you want to experiment with different parameter settings, you can reuse the preprocessed training data by using the `--cached` option - see [[Reusing preprocessed training data]]. Only the `analyzer`, `vocab` and `min_df` settings affect the preprocessing; you can use the `--cached` option as long as you haven't changed these parameters.
## Usage

Load a vocabulary:

    annif load-vocab yso /path/to/Annif-corpora/vocab/yso-en.tsv

Train the model:

    annif train omikuji-parabel-en /path/to/Annif-corpora/training/yso-finna-en.tsv.gz

Test the model with a single document:

    cat document.txt | annif suggest omikuji-parabel-en

Evaluate a directory full of files in fulltext [[document corpus|Document corpus formats]] format:

    annif eval omikuji-parabel-en /path/to/documents/