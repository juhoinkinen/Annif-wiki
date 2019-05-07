The `fasttext` backend implements a text classification algorithm based on word embeddings and machine learning. It is a wrapper around the [fastText library](https://fasttext.cc/) created by Facebook Research. The model resembles a feed-forward neural network with one hidden layer, though some shortcuts are used for computational efficiency.

The quality of results can be very good, but many parameters have to be selected to get optimal results. Training can be computationally intensive; by default it can train using up to 12 CPU cores in parallel.

## Installation

See [[Optional features and dependencies]]

## Example configuration

This is a simple configuration that creates a relatively small model (1.3GB when trained on the `yso-finna-fi` dataset):

```
[fasttext-en]
name=fastText English
language=en
backend=fasttext
analyzer=snowball(english)
dim=100
lr=0.25
epoch=5
loss=hs
limit=100
chunksize=24
vocab=yso-en
```

This is a more advanced configuration that creates a larger (3.6GB when trained on the `yso-finna-fi` dataset), but more accurate model which also takes much longer to train:

```
[yso-fasttext-fi]
name=YSO fastText Finnish
language=fi
backend=fasttext
analyzer=snowball(finnish)
dim=430
lr=0.74
epoch=75
minn=4
maxn=7
minCount=3
loss=hs
limit=1000
chunksize=24
vocab=yso-fi
```

### Backend-specific parameters

With the exception of `chunksize` and `limit`, all the parameters are passed directly to the fastText algorithm. If you omit a parameter, fastText will choose its default value. You can check out the [fastText documentation about options](https://fasttext.cc/docs/en/options.html) for more details about those parameters.

The backend processes longer documents in chunks: the document is represented as a list of sentences, and that list is turned into chunks. With a `chunksize` of 24 (as above), each chunk is made of 24 sentences, except for the last chunk which may be shorter. Each chunk is analyzed separately and the results are averaged. Setting `chunksize` to a high value such as 10000 will in practice disable chunking.

The most important parameters are:

Parameter |  Description
-------- | --------------------------------------------------
chunksize | How many sentences per chunk
limit | Maximum number of results to return
dim | Dimensionality of word vectors (i.e. hidden layer size)
lr | Learning rate
epoch | How many passes over training data to perform
loss | Loss function: `ns`, `hs` or `softmax`. `hs` is much faster than the others.
minn | Lower limit of character n-gram length
maxn | Upper limit of character n-gram length
minCount | Minimum word (or n-gram) frequency to include it in the model

## Usage

Load a vocabulary:

    annif loadvoc fasttext-en /path/to/Annif-corpora/vocab/yso-en.tsv

Train the model:

    annif train fasttext-en /path/to/Annif-corpora/training/yso-finna-en.tsv.gz

Test the model with a single document:

    cat document.txt | annif suggest fasttext-en

Evaluate a directory full of files in fulltext [[document corpus|Document corpus formats]] format:

    annif eval fasttext-en /path/to/documents/