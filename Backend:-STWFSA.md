The `STWFSA` backend is a wrapper around [STWFSAPY](https://github.com/zbw/stwfsapy), a lexical algorithm based on finite state automata. 

It achieves best results with short texts, i.e., titles and author keywords, and is best suited for English language data.


## Example configuration

A minimal configuration:

```
[stwfsa-yso-en]
name=STWFSA YSO english
language=en
backend=stwfsa
vocab=yso-en
concept_type_uri=http://www.w3.org/2004/02/skos/core#Concept
```

A configuration making use of hierarchical structure in the vocabulary.
```
[stwfsa-stw-en]
name=STWFSA STW english
language=en
backend=stwfsa
vocab=stw2
concept_type_uri=http://zbw.eu/namespaces/zbw-extensions/Descriptor
sub_thesaurus_type_uri=http://zbw.eu/namespaces/zbw-extensions/Thsys
thesaurus_relation_type_uri=http://www.w3.org/2004/02/skos/core#broader
thesaurus_relation_is_specialisation=False
```

### Backend-specific parameters

The parameters are:

Parameter |  Description
-------- | --------------------------------------------------
concept_type_uri| The type of the concepts in your graph.
sub_thesaurus_type_uri | Optional type for sub thesaurus structure in your vocabulary.
thesaurus_relation_type_uri | Optional relation between sub thesauri and concepts.
thesaurus_relation_is_specialisation | Set to true if `thesaurus_relation_type_uri` is a specialisation.
remove_deprecated | Whether to remove deprecated concepts, enabled by default.
handle_title_case | Disable to not match title case versions of concept labels, disabled by default.
extract_upper_case_from_braces | Removes the explanation in braces from labels. I.e.m `GDP (Gross Domestic Product)` will be transformed to `GDP`
extract_any_case_from_braces | Can extract content of braces in Labels. I.e., `R&D (research and discovery)` will be transformed to `research and discovery`. Disabled by default
expand_ampersand_with_spaces | For labels that contain an ampersand it will also match text containing spaces around that symbol. I.e., `R & D` will be matched for label `R&D`. Enabled by default.
expand_abbreviation_with_punctuation | For labels containing only uppercase letters it will also match text with punctuation added. I.e., `G.D.P.` for label `GDP`. Enabled by default.
simple_english_plural_rules| Can detect simple english plural forms of labels. Disabled by default


If your vocabulary has a hierarchical structure you can use the parameters `sub_thesaurus_type_uri`, `thesaurus_relation_type_uri` and `thesaurus_relation_is_specialisation`. This will help the algorithm to detect structure in the vocabulary where it performs better than in others.

## Usage

Load a vocabulary:

    annif loadvoc stwfsa-yso-en /path/to/Annif-corpora/vocab/yso-en.tsv

Train the model:

    annif train stwfsa-yso-en /path/to/Annif-corpora/training/yso-finna-en.tsv.gz

Test the model with a single document:

    cat document.txt | annif suggest stwfsa-yso-en

Evaluate a directory full of files in fulltext [[document corpus|Document corpus formats]] format:

    annif eval stwfsa-yso-en /path/to/documents/