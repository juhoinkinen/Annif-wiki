The `STWFSA` backend is a wrapper around [STWFSAPY](https://github.com/zbw/stwfsapy), a lexical algorithm based on finite state automata. 

It is best suited for English language data.


## Example configuration

A minimal configuration:

```
[stwfsa-yso-en]
name=STWFSA YSO english
language=en
backend=stwfsa
vocab=yso-en
```

A configuration using custom classes for concept types, sub-thesauri and their relations (see parameter documentation below for more information).
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
concept_type_uri| The type of the concepts in your graph, defaults to `http://www.w3.org/2004/02/skos/core#Concept`.
sub_thesaurus_type_uri | Optional type for sub-thesaurus structure in your vocabulary, defaults to `http://www.w3.org/2004/02/skos/core#Collection`.
thesaurus_relation_type_uri | Optional type of relation between concepts and sub-thesaurus entries. Defaults to `http://www.w3.org/2004/02/skos/core#member`
thesaurus_relation_is_specialisation | Set to false if `thesaurus_relation_type_uri` is a specialisation relation instead of a generalisation relation. I.e., if you have (_concept_, `thesaurus_relation_type_uri`, _sub-thesaurus_) triples in your graph it should be set to `False`. Conversely it should be set to `True` if you have (_sub-thesaurus_, `thesaurus_relation_type_uri`, _concept_) in your graph. Defaults to `True`.
remove_deprecated | Whether to remove deprecated concepts, enabled by default.
handle_title_case | Disable to not match title case versions of concept labels, disabled by default.
extract_upper_case_from_braces | Removes the explanation in braces from labels. I.e., `GDP (Gross Domestic Product)` will be transformed to `GDP`
extract_any_case_from_braces | Can extract content of braces in labels. I.e., `R&D (research and discovery)` will be transformed to `research and discovery`. In contrast to `extract_upper_case_from_braces` it will extract the part inside the parenthesis and not the part before. Disabled by default.
expand_ampersand_with_spaces | For labels that contain an ampersand it will also match text containing spaces around that symbol. I.e., `R & D` will be matched for label `R&D`. Enabled by default.
expand_abbreviation_with_punctuation | For labels containing only uppercase letters it will also match text with punctuation added. I.e., `G.D.P.` for label `GDP`. Enabled by default.
simple_english_plural_rules| Can detect simple English plural forms of labels. Disabled by default.


If your vocabulary has a hierarchical structure you can use the parameters `sub_thesaurus_type_uri`, `thesaurus_relation_type_uri` and `thesaurus_relation_is_specialisation`. This will help the algorithm detect structure in the vocabulary that it can exploit.

## Usage

Load a vocabulary:

    annif loadvoc stwfsa-yso-en /path/to/Annif-corpora/vocab/yso-en.tsv

Train the model:

    annif train stwfsa-yso-en /path/to/Annif-corpora/training/yso-finna-en.tsv.gz

Test the model with a single document:

    cat document.txt | annif suggest stwfsa-yso-en

Evaluate a directory full of files in fulltext [[document corpus|Document corpus formats]] format:

    annif eval stwfsa-yso-en /path/to/documents/

## Provenance / contact information

[STWFSAPY](https://github.com/zbw/stwfsapy) has been developed as part of the automatization of subject indexing effort at ZBW – Leibniz Information Centre for Economics, [AutoSE](https://www.zbw.eu/en/about-us/key-activities/automated-subject-indexing).

If you have questions about the algorithm or about AutoSE in general, please write to <autose@zbw.eu>.