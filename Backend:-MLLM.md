The `mllm` backend is an implementation of the MLLM algorithm, which stands for Maui-like Lexical Matching. It is a Python reimplementation of many ideas that were used in the Maui algorithm (see [Medelyan, 2019](https://hdl.handle.net/10289/3513)), with some adjustments. It is meant for long full-text documents and like Maui, it needs to be trained with a relatively small number (hundreds or thousands) of manually indexed documents so that the algorithm can choose the right mix of heuristics that achieves best results on a particular document collection. The MLLM algorithm relies on the same [Analyzer](https://github.com/NatLibFi/Annif/wiki/Analyzers) functionality as most other Annif backends and should thus work for all languages for which a suitable analyzer exists.

Since the algorithm has not been described elsewhere, a short explanation follows. The general idea is the same as Maui, but some details are different. In empirical tests, the MLLM algorithm has so far performed as well as or better than Maui in terms of the common quality measures (precision, recall, F1 score, NDCG).

During training, the vocabulary is first processed and an index is constructed from all the terms (preferred, alternate and optionally also hidden labels). Additional data structures are initialized based on vocabulary structure: matrices are constructed from semantic relations between concepts (broader, narrower and related relationships) as well as members of SKOS Collections. Then the manually indexed example documents are processed, matching them with terms from the vocabulary via the term index. Each document will produce a number of matches; these are then consolidated into a set of candidate subjects for that document, and heuristic feature values will be calculated for each candidate; the heuristics rely, in part, on the vocabulary structure as well as more general features such as TF-IDF value, document length and spread. Finally, a machine learning classifier (an ensemble of 10 decision trees) is trained to predict which candidate features indicate that the subject is appropriate for the document, based on the manually assigned subjects. The indexes and the trained classifier are then saved to disk; together they constitute the trained MLLM model.

During inference (use on new documents), the same process is repeated for each document. The terms in the document are matched against vocabulary terms, the matches are consolidated into candidate subjects, heuristics are used to calculate feature values for each candidate, and the classifier is used to predict the most likely subjects based on the feature values.

The main differences between MLLM and Maui are:

* the lexical matching process in MLLM is based on sets of (normalized) tokens instead of word n-grams; in practice, matching in MLLM is less strict than in Maui, leading to higher recall at the cost of precision;
* Maui has only one feature for semantic relatedness, while MLLM uses distinct heuristics for broader, narrower and related relationships
* MLLM includes features that are not used in Maui:
  * membership in the same SKOS Collection
  * matched via preferred label
  * ambiguity: whether the set of matched terms could also have been matched with other subjects
* MLLM does not implement all the features in Maui, for example Wikipedia related features that are only useful for keyword extraction without a controlled vocabulary

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

The backend takes some optional parameters, mainly for the classifier.

Parameter |  Description
-------- | --------------------------------------------------
min_samples_leaf |
max_leaf_nodes | 
max_samples | 
use_hidden_labels | Match concepts based on their hidden labels (in addition to preferred and alternate labels). Defaults to false


## Usage

Load a vocabulary:

    annif loadvoc yso-mllm-en /path/to/Annif-corpora/vocab/yso-en.tsv

Train the model:

    annif train yso-mllm-en /path/to/Annif-corpora/training/yso-finna-en.tsv.gz

Test the model with a single document:

    cat document.txt | annif suggest yso-mllm-en

Evaluate a directory full of files in fulltext [[document corpus|Document corpus formats]] format:

    annif eval yso-mllm-en /path/to/documents/