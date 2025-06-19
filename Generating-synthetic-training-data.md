A common challenge when starting to use Annif with a new vocabulary is the lack of sufficient amount of training data, i.e. documents that are already indexed with the subjects from the vocabulary.
One way to address the problem is to augment the existing data by generating additional synthetic data with large language models (LLMs). This approach was successfully used in [the SemEval-2025 task 5: LLMs4Subjects](https://arxiv.org/abs/2504.19675) and for GermEval-2025 (paper TBA).

The basic idea is to use an LLM to generate new documents based on existing ones. For each training document with manually assigned subject labels, the LLM is prompted to create a similar document using the same set of subjects, plus one additional, randomly selected subject from the vocabulary. This process can be repeated for each training document, and multiple synthetic documents can be generated per original if needed.

The prompt template used for SemEval-2025 task is as follows:

> You are a professional metadata manager.
>
> Your task is to create new bibliographic metadata: document titles and descriptions.
>
> Here is an example document title and description in <LANGUAGE> with the following subject keywords: <OLD_KEYWORDS>
>
> <TITLE_DESC>
>
> Generate a new document title and description in <LANGUAGE>. Respond with only the title and description, nothing else. Create a new title and description that match the following subject keywords: <NEW_KEYWORDS>

The scripts used in generation are available [on GitHub](https://github.com/NatLibFi/Annif-LLMs4Subjects/tree/143f88cd2c45cbbb970fb49f81527d113dd7e90e/tools/synthetic-data).

To add variation to the generated documents, different LLMs can be used for the each of the generation steps for a document, as performed for the GermEval-2025 (paper TBA).

It is important to analyze the impact of the synthetic data to the model performance by evaluating it against an independent test set.
Typically, performance improves with more synthetic data up to a point, after which it starts to plateau.
A final performance boost can often be achieved by including the original, manually labeled dataset twice in the training process.
This helps the model better align with real-world data and prevents overfitting to synthetic examples.

[[/images/effect-of-synthetic-data.png|Effect of synthetic-data]]
Learning curve showing the effect of adding synthetic training data for Omikuji Bonsai models (Figure 3 in the [SemEval-2025 paper](https://arxiv.org/html/2504.19675v1#A1)).

---
[[← Reusing preprocessed training data|Reusing-preprocessed-training-data]] | [[Backends →|Backends]]