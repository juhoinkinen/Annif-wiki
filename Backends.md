Backends in Annif are the way to use the algorithms responsible for performing automated subject indexing. Each backend has its own strengths, requirements and ideal use cases.

The below table aims to provide the basic information of all the backends.

| Name | Type | Requires extra deps. | Description | # train docs. | Train docs. length | Supports hyperopt |
|-------------|-------------|-----|-------------------------------------------------------------------------------------------------------------|--------------|------------|-----|
| TF-IDF      | Associative | No  | A baseline algorithm, _only for setup testing_.                                                             | ?            | short/long | No  |
| fastText    | Associative | Yes | Allows to use word and character level n-grams (i.e. words that appear together and subwords).              | 10,000+      | short/long | No  |
| Omikuji     | Associative | Yes | A tree-based algorithm for extreme multilabel classification.                                               | 10,000+      | short/long | No  |
| SVC         | Associative | No  | Linear Support Vector Classification for multiclass (_not multilabel_) classification.                      | ?            | ?          | No  |
| MLLM        | Lexical     | No  | Maui-like lexical matching.                                                                                 | 100-10,000   | long       | Yes |
| Ensemble    | Ensemble    | No  | Combines results from multiple backends with averaging.                                                     | NA           | NA         | Yes |
| NN-ensemble | Ensemble    | Yes | Combines results from multiple backends using a neural network.                                             | 1,000-10,000 | long       | No  |
| PAV         | Ensemble    | No  | A trainable dynamic ensemble that intelligently combines results from multiple projects.                    | ?            | ?          | No  |
| YAKE        | Lexical     | Yes | An unsupervised keyword extraction method applied to find vocabulary terms. Requires no training.           | NA           | long       | No  |
| STWFSA      | Lexical     | Yes | Statistical translation weighted finite state automaton.                                                    | ?            | short/long | No  |
| HTTP        | Special     | No  | Communicates with a REST API that provides a suggest method, e.g. with another instance of Annif.           | NA           | NA         | No  |
| Dummy       | Special     | No  | Returns fixed results (used for internal testing).                                                          | NA           | NA         | NA  |

<!---
| Xtransformer| Stastical   | Yes             | Utilizes pre-trained and then finetuned transformer models and hierarchical label tree.                     | x                   | high  | No                |
| LLM-ensemble| Ensemble    | Yes             | Uses an LLM to rate the subject suggestions by the source projects.                                         | NA                  |       | No                |
--->

Column Descriptions:
- Name: The identifier of the backend.
- Type: The general approach used by the backend:
    - Associative: Learns associations between input text and subject terms from training data.
    - Lexical: Uses lexical matching techniques.
    - Ensemble: Combines results from multiple backends.
    - Special: Provides utility or integration features.
- Requires extra deps.: Indicates whether additional dependencies must be installed in addition to the base Annif installation. See [[Optional-features-and-dependencies]] for details.
- Description: A short explanation of the backend’s functionality and purpose.
- \# Train Docs.: Recommended number of training documents for good performance.
- Train Docs. Length: Indicates whether the backend works best with
    - short texts (e.g., titles),
    - long texts (e.g., abstracts or full documents) or
    - both.
- Supports Hyperopt: Whether the backend supports hyperparameter optimization using Annif’s hyperopt feature. The optimized parameters are
    - the hyperparameters of the tree algorithm with the MLLM backend
    - the source project weights with the ensemble backend
