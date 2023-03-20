A subject vocabulary defines the subjects available for automated indexing or classification. Typically this will be a thesaurus, a classification or a list of subject headings. Annif doesn't care much about the internal structure of a subject vocabulary, it just needs to know the URIs and preferred labels (a.k.a. terms or descriptors) of each subject/class/concept. If the vocabulary includes also notion codes, e.g. as in [UDC](https://en.wikipedia.org/wiki/Universal_Decimal_Classification), also they can be given.

## Subject vocabulary as TSV

The simple TSV subject vocabulary format only specifies URIs and labels for concepts and only supports one language. The vocabulary file is UTF-8 encoded TSV (tab separated values) file with the file extension `.tsv`, where the first column contains a subject URI and the second column its label (and the optional third column the notation code). The format is the same as the extended subject file format for documents, specified below. For example:

```
<http://example.org/thesaurus/subj1>	networking
<http://example.org/thesaurus/subj2>	computer science
<http://example.org/thesaurus/subj3>	Internet Protocol	42.42

## Subject vocabulary as CSV

The CSV subject vocabulary format only specifies URIs and labels for concepts; labels can be given in many languages. The vocabulary file is UTF-8 encoded CSV (comma separated values) file with the file extension `.csv`. The first row is a header which defines the meaning of columns in subsequent rows. The header must contain a column called `uri` and one or more columns called `label_XX`, where `XX` is a BCP47 language tag such as `en` or `fr`. There may also be a `notation` column for notations. Example:

```
uri,label_en,notation
http://example.org/thesaurus/subj1,networking,
http://example.org/thesaurus/subj2,computer science,
http://example.org/thesaurus/subj3,Internet Protocol,42.42
```

## Subject vocabulary as SKOS

A subject vocabulary can also be given as a SKOS/RDF file. All common RDF serializations (i.e. those supported by rdflib) are supported, including RDF/XML, Turtle and N-Triples.