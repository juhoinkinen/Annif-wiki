# DEPRECATION NOTE: THIS BACKEND WILL NO LONGER BE AVAILABLE IN ANNIF 0.56

The `vw_multi` backend is a wrapper around the multiclass and multilabel classification algorithms (`oaa`, `ect`, `log_multi` and `multilabel_oaa`) implemented by the [Vowpal Wabbit](https://github.com/VowpalWabbit/vowpal_wabbit) machine learning system. It is probably best suited for classification tasks with a relatively small number of classes/subjects (fewer than 1000) though this depends on the specific algorithm and its parameters.

This backend also supports [online learning](https://en.wikipedia.org/wiki/Online_machine_learning), which means that it can be further trained during use via the `learn` command in the Annif CLI and REST API.

## Installation

See [[Optional features and dependencies]]

## Example configuration

This is a simple configuration that uses the default `oaa` algorithm:

```
[vw-multi-en]
name=vw_multi English
language=en
backend=vw_multi
analyzer=snowball(english)
bit_precision=22
probabilities=1
limit=100
chunksize=2
vocab=yso-en
```

This is a more advanced configuration that uses the `ect` algorithm with a `logistic` loss function, multiple passes during training and word bigrams (n-grams of two consecutive words):

```
[vw-multi-ect-en]
name=vw_multi ECT English
language=en
backend=vw_multi
analyzer=snowball(english)
algorithm=ect
loss_function=logistic
passes=10
bit_precision=22
limit=100
ngram=2
chunksize=2
vocab=yso-en
```

### Backend-specific parameters

With the exception of `chunksize` and `limit`, the parameters are passed directly to the selected VW algorithm. If you omit a parameter, a default value is used. 

Parameter | Description
--------- | --------------------------------------------------
chunksize | How many sentences per chunk
limit | Maximum number of results to return
ngram |	maximum length of word n-grams (default: 1)
bit_precision | Determines size of feature space. VW default is rather low, usually 22-28 are better.
learning_rate | Learning rate
loss_function | Function used when training model. Valid values: `squared`, `logistic`, `hinge`
l1 | L1 regularization lambda value (usually a small float value)
l2 | L2 regularization lambda value (usually a small float value)
passes | How many passes over training data to perform
probabilities | If set to 1, return probabilities instead of binary 1/0. Only supported by the `oaa` algorithm

You can check out the [VW documentation about command line arguments](https://github.com/VowpalWabbit/vowpal_wabbit/wiki/Command-line-arguments) for more details about the parameters.

The backend processes longer documents in chunks: the document is represented as a list of sentences, and that list is turned into chunks. With a `chunksize` of 2 (as above), each chunk is made of 2 sentences, except for the last chunk which may be shorter. Each chunk is analyzed separately and the results are averaged. Setting `chunksize` to a high value such as 10000 will in practice disable chunking.

### Probabilities setting

VW multiclass and multilabel algorithms usually only return either a single class or, in the case of `multilabel_oaa`, a set of classes, but no scores or probabilities for the class(es). This differs from most Annif backends which return a distribution of scores. However, with a small chunk size, the class will be determined for each chunk separately and those results are averaged over the whole document, so the result can still be a distribution of scores.

The VW `oaa` algorithm, however, can optionally output class probabilities. You can enable this using `probabilities=1` in the backend configuration. See [Predicting probabilities](https://github.com/VowpalWabbit/vowpal_wabbit/wiki/Predicting-probabilities) in the VW wiki for some more details.

## Retraining with cached training data

Preprocessing the training data can take a significant portion of the training time. If you want to experiment with different parameter settings, you can reuse the preprocessed training data by using the `--cached` option - see [[Reusing preprocessed training data]]. Only the `analyzer` and `vocab` settings affect the preprocessing; you can use the `--cached` option as long as you haven't changed these parameters.

## Usage

Load a vocabulary:

    annif loadvoc vw-multi-en /path/to/Annif-corpora/vocab/yso-en.tsv

Train the model:

    annif train vw-multi-en /path/to/Annif-corpora/training/yso-finna-en.tsv.gz

Learn from additional documents:

    annif learn vw-multi-en /path/to/documents.tsv

Test the model with a single document:

    cat document.txt | annif suggest vw-multi-en

Evaluate a directory full of files in fulltext [[document corpus|Document corpus formats]] format:

    annif eval vw-multi-en /path/to/documents/