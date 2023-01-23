The `nn_ensemble` backend implements a trainable dynamic ensemble that intelligently combines results from multiple projects. Subject suggestion requests to the ensemble backend will be re-routed to the source projects. The results from the source projects will be re-weighted using a neural network implemented using [Keras](https://keras.io/) and [TensorFlow](https://www.tensorflow.org/) 2. Initially (with no training), the backend behaves exactly like a basic [[ensemble|Backend: ensemble]] backend, but the training data is then used to fine-tune the output to better match the given manually assigned subjects in the training documents.

This backend is similar in spirit to the [[PAV|Backend: PAV]] backend, but unlike PAV, this backend also supports [online learning](https://en.wikipedia.org/wiki/Online_machine_learning), which means that it can be further trained during use via the `learn` command in the Annif CLI and REST API.

TensorFlow may be able to utilize a GPU if the system has one available - see the TensorFlow documentation for details. In practice, when training an ensemble, the great majority of processing time is typically spent processing the document in the source projects, so the extra benefit of a GPU is likely to be minimal.

See also the [Annif-tutorial exercise about NN ensemble project](https://github.com/NatLibFi/Annif-tutorial/blob/master/exercises/OPT_nn_ensemble_project.md).

## Installation

See [[Optional features and dependencies]]

## Example configuration

```
[nn-ensemble-en]
name=NN ensemble English
language=en
backend=nn_ensemble
sources=tfidf-en,mllm-en
limit=100
vocab=yso
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
lr | The learning rate of the optimizer. Default depends on the optimizer
lmdb_map_size | Maximum size of the LMDB database in bytes. The default is 1073741824 i.e. 1GB.

The `nodes` setting determines the size of the neural network. Larger networks take up more memory, but may provide better results in some cases. The `dropout_rate` setting affects the amount of dropout regularization to apply in order to prevent overfitting. The `epochs` setting affects how much the network tries to learn from the initial training data (given using the `train` command). Some experimentation is necessary to find the optimal parameters.

The `sources` setting is a comma-separated list of projects whose results will be combined. Optional weights may be given like this:

    sources=tfidf-en:1,mllm-en:2

This setting would give twice as much weight on results from `mllm-en` compared to results from `tfidf-en`.

## Notes on LMDB storage and memory usage

This backend stores the preprocessed training data in an [LMDB](https://en.wikipedia.org/wiki/Lightning_Memory-Mapped_Database) database called `nn-train.mdb` under its project directory. This saves RAM by avoiding keeping large vectors in memory at once and also allows the training data to be reused (see below). The LMDB database is a [memory-mapped file](https://en.wikipedia.org/wiki/Memory-mapped_file) which appears as a 1GB file (by default, unless adjusted with the `lmdb_map_size` parameter) in a typical directory listing, but in fact most of the file is usually empty and it actually takes up much less disk space (check with `du -s` if you're curious). Also, the memory usage (typically measured as Resident Set Size, RSS) of the Annif process accessing the LMDB database may seem high, but due to the memory mapping, that memory can be freed at any time, so the RAM is not actually exclusively reserved to the process. (See [more detailed explanation](https://symas.com/understanding-lmdb-database-file-sizes-and-memory-utilization/) of LMDB file sizes and memory usage) Don't be scared if you think you are seeing excessive disk and/or memory usage!

For extremely large training sets, you may get a `lmdb.MapFullError`. The solution is to increase the value of the `lmdb_map_size` setting.

## Retraining with cached training data

Preprocessing the training data (which in the context of this backend means running all the training documents through the source projects) can take a significant portion of the training time. If you want to experiment with different parameter settings, you can reuse the preprocessed training data by using the `--cached` option - see [[Reusing preprocessed training data]]. Only the `sources` and `vocab` settings affect the preprocessing; you can use the `--cached` option as long as you haven't changed these parameters. Even if you have only changed the weights of the source projects, you cannot use `--cached` but will need to retrain from scratch.

## Usage

Load a vocabulary:

    annif load-vocab yso /path/to/Annif-corpora/vocab/yso-skos.ttl

Train the ensemble:

    annif train nn-ensemble-en /path/to/Annif-corpora/training/yso-finna-en.tsv.gz

Test the model with a single document:

    cat document.txt | annif suggest nn-ensemble-en

Learn from additional documents:

    annif learn nn-ensemble-en /path/to/documents.tsv

Evaluate a directory full of files in fulltext [[document corpus|Document corpus formats]] format:

    annif eval nn-ensemble-en /path/to/documents/