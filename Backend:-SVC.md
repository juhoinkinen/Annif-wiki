The SVC that implements Linear Support Vector Classification. It is based on [LinearSVC](https://scikit-learn.org/stable/modules/generated/sklearn.svm.LinearSVC.html) in scikit-learn, which in turn is based on liblinear. This kind of algorithm is well suited for multiclass (but not multilabel) classification, for example classifying documents with the Dewey Decimal Classification or the [20 Newsgroups](http://qwone.com/~jason/20Newsgroups/) classification, which is used for the examples below. It requires relatively little training data. It is suitable for classifications of up to around 10,000 classes.

Note that SVC cannot handle more than one class/subject per document during training. If a document in the training corpus has more than one subject, a random one of the subjects is picked and a warning is shown.

## Example configuration

```
[svc-en]
name=SVC English
language=en
backend=svc
analyzer=snowball(english)
limit=100
vocab=20news
```

### Backend-specific parameters

The parameters are:

Parameter |  Description
-------- | --------------------------------------------------
limit | Maximum number of results to return
min_df | How many documents a word must appear in to be considered. Default: 1
ngram | maximum length of word n-grams (default: 1)

The `min_df` parameter controls the features (words/tokens) used to build the model. With the default setting of 1, all the words in the training set will be used, even ones that appear in only one training document. With a higher value such as 5, only those that appear in at least that many documents are included. Increasing the `min_df` value will decrease the size and training time of the model.

The `ngram` parameter controls the use of word n-grams. With the default setting (1), only single words are considered. Setting this to 2 will use word bigrams, which may increase accuracy, but the model will be much larger.

## Usage

Load a vocabulary:

    annif load-vocab 20news /path/to/Annif-corpora/fulltext/20news/20news-vocab.tsv

Train the model:

    annif train svc-en /path/to/Annif-corpora/fulltext/20news/20news-train.tsv

Test the model with a single document:

    cat document.txt | annif suggest svc-en

Evaluate a files in short text [[document corpus|Document corpus formats]] format:

    annif eval svc-en /path/to/Annif-corpora/fulltext/20news/20news-test.tsv