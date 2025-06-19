Annif includes multiple backends that implement or use (via external libraries) algorithms for automated subject indexing. Each backend has its own strengths, requirements, and ideal use cases. The table below provides a quick comparison—see each backend's page for details and configuration options.

> [!TIP]
> For a quick setup, start with TF-IDF; for best results, try fastText, Omikuji and MLLM combined in ensembles.

The below table aims to provide the basic information of all the backends.

| Name | Type | Requires extra dependencies | Description | # train documents | Train documents length | Supports hyperopt |
|-------------|-------------|-----|-------------------------------------------------------------------------------------------------------------|--------------|------------|-----|
| [TF-IDF](https://github.com/NatLibFi/Annif/wiki/Backend%3A-TF-IDF)      | Associative | No  | A baseline algorithm, _only for setup testing_.                                                             | ?            | short/long | No  |
| [fastText](https://github.com/NatLibFi/Annif/wiki/Backend%3A-fastText)    | Associative | Yes | Allows to use word and character level n-grams (i.e. words that appear together and subwords).              | 10,000+      | short/long | No  |
| [Omikuji](https://github.com/NatLibFi/Annif/wiki/Backend%3A-Omikuji)     | Associative | Yes | A tree-based algorithm for extreme multilabel classification.                                               | 10,000+      | short/long | No  |
| [SVC](https://github.com/NatLibFi/Annif/wiki/Backend%3A-SVC)         | Associative | No  | Linear Support Vector Classification for multiclass (_not multilabel_) classification.                      | ?            | ?          | No  |
| [MLLM](https://github.com/NatLibFi/Annif/wiki/Backend%3A-MLLM)        | Lexical     | No  | Maui-like lexical matching.                                                                                 | 100-10,000   | long       | Yes |
| [Ensemble](https://github.com/NatLibFi/Annif/wiki/Backend%3A-Ensemble)    | Ensemble    | No  | Combines results from multiple backends with averaging.                                                     | NA           | NA         | Yes |
| [NN-ensemble](https://github.com/NatLibFi/Annif/wiki/Backend%3A-nn_ensemble) | Ensemble    | Yes | Combines results from multiple backends using a neural network.                                             | 1,000-10,000 | long       | No  |
| [PAV](https://github.com/NatLibFi/Annif/wiki/Backend%3A-PAV)         | Ensemble    | No  | A trainable dynamic ensemble that intelligently combines results from multiple projects.                    | ?            | ?          | No  |
| [YAKE](https://github.com/NatLibFi/Annif/wiki/Backend%3A-YAKE)        | Lexical     | Yes | An unsupervised keyword extraction method applied to find vocabulary terms. Requires no training.           | NA           | long       | No  |
| [STWFSA](https://github.com/NatLibFi/Annif/wiki/Backend%3A-STWFSA)      | Lexical     | Yes | Statistical translation weighted finite state automaton.                                                    | ?            | short/long | No  |
| [HTTP](https://github.com/NatLibFi/Annif/wiki/Backend%3A-HTTP)        | Special     | No  | Communicates with a REST API that provides a suggest method, e.g. with another instance of Annif.           | NA           | NA         | No  |
| [Dummy](https://github.com/NatLibFi/Annif/wiki/Backend%3A-Dummy)       | Special     | No  | Returns fixed results (used for internal testing).                                                          | NA           | NA         | NA  |

<!---
| Xtransformer| Stastical   | Yes             | Utilizes pre-trained and then finetuned transformer models and hierarchical label tree.                     | x                   | high  | No                |
| LLM-ensemble| Ensemble    | Yes             | Uses an LLM to rate the subject suggestions by the source projects.                                         | NA                  |       | No                |
--->

## Column descriptions
- Name: The identifier of the backend.
- Type: The general approach used by the backend:
    - Associative: Learns associations between input text and subject terms from training data.
    - Lexical: Uses lexical matching techniques.
    - Ensemble: Combines results from multiple backends.
    - Special: Provides utility or integration features.
- Requires extra dependencies: Indicates whether additional dependencies must be installed in addition to the base Annif installation. See [[Optional-features-and-dependencies]] for details.
- Description: A short explanation of the backend’s functionality and purpose.
- \# Train documents: Recommended number of training documents for good performance.
- Train documents length: Indicates whether the backend works best with
    - short texts (e.g., titles),
    - long texts (e.g., abstracts or full documents) or
    - both.
- Supports hyperopt: Whether the backend supports hyperparameter optimization using `annif hyperopt` command. The optimized parameters are
    - the hyperparameters of the tree algorithm with the MLLM backend
    - the source project weights with the ensemble backend

---
[[← Generating synthetic training data|Generating-synthetic-training-data]] | [[Development flow, branches and tags →|Development-flow,-branches-and-tags]]
