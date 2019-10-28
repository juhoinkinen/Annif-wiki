The `nn_ensemble` backend implements a trainable dynamic ensemble that intelligently combines results from multiple projects. Subject suggestion requests to the ensemble backend will be re-routed to the source projects. The results from the source projects will be re-weighted using a neural network implemented using [Keras](https://keras.io/) and [TensorFlow](https://www.tensorflow.org/) 2. Initially (with no training), the backend behaves exactly like a basic [[ensemble|Backend: ensemble]] backend, but the training data is then used to fine-tune the output to better match the given manually assigned subjects in the training documents.

This backend is similar in spirit to the [[PAV|Backend: PAV]] backend, but unlike PAV, this backend also supports [online learning](https://en.wikipedia.org/wiki/Online_machine_learning), which means that it can be further trained during use via the `learn` command in the Annif CLI and REST API.

TensorFlow may be able to utilize a GPU if the system has one available - see the TensorFlow documentation for details. In practice, when training an ensemble, the great majority of processing time is typically spent processing the document in the source projects, so the extra benefit of a GPU is likely to be minimal.

## Installation

See [[Optional features and dependencies]]

## Example configuration

```
[nn-ensemble-en]
name=NN ensemble English
language=en
backend=nn_ensemble
sources=tfidf-en,maui-en
limit=100
vocab=yso-en
nodes=100
dropout_rate=0.2
epochs=10
```

### Backend-specific parameters

The following backend-specific parameters are supported:

Parameter | Description
--------- | --------------------------------------------------
nodes | The number of nodes (neurons) in the hidden layer of the neural network. Defaults to 100
dropout_rate | The amount of dropout to apply between layers (between 0.0 and 1.0) during training. Defaults to 0.2
optimizer | The optimizer to use. Defaults to "adam"
epochs | The number of passes over the initial training data to perform

The `nodes` setting determines the size of the neural network. Larger networks take up more memory, but may provide better results in some cases. The `dropout_rate` setting affects the amount of dropout regularization to apply in order to prevent overfitting. The `epochs` setting affects how much the network tries to learn from the initial training data (given using the `train` command). Some experimentation is necessary to find the optimal parameters.

The `sources` setting is a comma-separated list of projects whose results will be combined. Optional weights may be given like this:

    sources=tfidf-en:1,maui-en:2

This setting would give twice as much weight on results from `maui-en` compared to results from `tfidf-en`.

## Usage

Load a vocabulary:

    annif loadvoc nn-ensemble-en /path/to/Annif-corpora/vocab/yso-en.tsv

Train the ensemble:

    annif train nn-ensemble-en /path/to/Annif-corpora/training/yso-finna-en.tsv.gz

Test the model with a single document:

    cat document.txt | annif suggest nn-ensemble-en

Learn from additional documents:

    annif learn nn-ensemble-en /path/to/documents.tsv

Evaluate a directory full of files in fulltext [[document corpus|Document corpus formats]] format:

    annif eval nn-ensemble-en /path/to/documents/