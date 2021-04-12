The `mllm` backend is an implementation of the MLLM algorithm, which stands for Maui-like Lexical Matching. It is a Python reimplementation of many ideas that were used in the Maui algorithm (see [Medelyan, 2019](https://hdl.handle.net/10289/3513)), with some adjustments. It is meant for long full-text documents and like Maui, it needs to be trained with a relatively small number (hundreds or thousands) of manually indexed documents so that the algorithm can choose the right mix of heuristics that achieves best results on a particular document collection. The MLLM algorithm relies on the same [Analyzer](https://github.com/NatLibFi/Annif/wiki/Analyzers) functionality as most other Annif backends and should thus work for all languages for which a suitable analyzer exists.

Since the algorithm has not been described elsewhere, a short explanation follows. The general idea is the same as Maui, but some details are different. In empirical tests, the MLLM algorithm has so far performed as well as or better than Maui in terms of the common quality measures (precision, recall, F1 score, NDCG).

The following steps happen during training:

1. A term index is constructed from all the terms (preferred, alternate and optionally also hidden labels)
2. Additional indexes are initialized based on vocabulary structure: semantic relations between concepts (broader, narrower and related) as well as members of SKOS Collections
3. The manually indexed training documents are processed, matching them with terms from the vocabulary via the term index constructed in step 1; the result is a set of matches within each document
4. The matches for each document are combined into candidate subjects; repeated matches for the same subject will be combined into a single candidate
5. Numeric features are calculated for each candidate, based on heuristics that rely on general features such as document length, TF-IDF value and spread, as well as the vocabulary indexes from step 2
6. A machine learning classifier (an ensemble of 10 decision trees) is trained to predict which candidate features indicate that the subject is appropriate for the document, by comparing the candidates with the manually assigned subjects for the documents.
7. The indexes and the classifier are saved to disk; together they constitute the trained MLLM model.

During inference (use on new documents), steps 3-5 are repeated for each document: The terms in the document are matched against vocabulary terms, the matches are consolidated into candidate subjects, and heuristics are used to calculate feature values for each candidate. Finally, the classifier is used to predict the most likely subjects based on the feature values.

The main differences between MLLM and Maui are:

* Maui can be used for both keyphrase extraction (without a controlled vocabulary) and subject indexing (with a vocabulary). MLLM assumes that there is a defined vocabulary and only does the latter 
* the lexical matching process in MLLM is based on sets of (normalized) tokens instead of word n-grams; in practice, matching in MLLM is less strict than in Maui, leading to higher recall at the cost of precision;
* Maui has only one feature for semantic relatedness, while MLLM uses distinct heuristics for broader, narrower and related relationships
* MLLM includes features that are not used in Maui:
  * membership in the same SKOS Collection
  * matched via preferred label vs. non-preferred (alternate or hidden) label
  * ambiguity: whether the set of matched tokens could also have been matched with other subjects
* MLLM does not implement all the features in Maui, for example Wikipedia related features that are only useful for keyword extraction without a controlled vocabulary
* Maui is a standalone Java application, while MLLM is implemented in Python as a part of the Annif code base and relies on some of the facilities that Annif provides, e.g. the configuration, vocabulary and analyzer functionality.

## Example configuration

A minimal configuration that relies on default values:

```
[yso-mllm-en]
language=en
backend=mllm
analyzer=snowball(english)
vocab=yso-en
```

### Backend-specific parameters

The backend takes some optional parameters. The `use_hidden_labels` controls whether hidden labels from the vocabulary are used in matching. The other parameters control the classifier; see the scikit-learn documentation for [DecisionTreeClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.tree.DecisionTreeClassifier.html) and [BaggingClassifier](https://scikit-learn.org/stable/modules/generated/sklearn.ensemble.BaggingClassifier.html?highlight=baggingclassifier) for details. It may not be obvious which classifier parameters produce the best results; the default values are a reasonable starting point, but better values may be found using hyperparameter optimization (see below).

Parameter |  Description
-------- | --------------------------------------------------
use_hidden_labels | Match concepts based on their hidden labels (in addition to preferred and alternate labels). Defaults to false.
min_samples_leaf | The minimum number of samples required to be at a leaf node. Defaults to 20.
max_leaf_nodes | Limit for the number of leaf nodes in the decision tree. Defaults to 1000.
max_samples | The fraction of samples to use for training each decision tree in the ensemble. Defaults to 0.9.

## Hyperparameter optimization

You can automate the search for the parameters using the `hyperopt` command. This requires that you have first trained the MLLM project on some training documents to initialize the data structures. You will also need a separate validation set of indexed documents (typically hundreds or thousands) that will be used to measure how good results a particular combination of parameter values will give. The validation set should not include any of the documents in the training set. Here is an example on how to try 100 different hyperparameter combinations, running 4 jobs in parallel on multiple CPU cores:

    annif hyperopt yso-mllm-en --trials 100 --jobs 4 /path/to/validation-set/

Hyperparameter optimization may take a very long time; this depends a lot on the size of the validation set. At the end, the best performing parameters are reported, and these can be copied to the configuration file. You can also use the `--results-file` option to save the results of individual trials into a CSV file for later analysis.

## Usage

Load a vocabulary:

    annif loadvoc yso-mllm-en /path/to/Annif-corpora/vocab/yso-en.tsv

Train the model:

    annif train yso-mllm-en /path/to/Annif-corpora/training/yso-finna-en.tsv.gz

Test the model with a single document:

    cat document.txt | annif suggest yso-mllm-en

Evaluate a directory full of files in fulltext [[document corpus|Document corpus formats]] format:

    annif eval yso-mllm-en /path/to/documents/