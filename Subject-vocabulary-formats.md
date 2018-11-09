A subject vocabulary defines the subjects available for automated indexing or classification. Typically this will be a thesaurus, a classification or a list of subject headings. Annif doesn't care much about the internal structure of a subject vocabulary, it just needs to know the URIs and preferred labels (a.k.a. terms or descriptors) of each subject/class/concept.

## Subject vocabulary as TSV

The simple TSV subject vocabulary format only specifies URIs and labels for concepts. The vocabulary file file is UTF-8 encoded TSV (tab separated values) file with the file extension `.tsv`, where the first column contains a subject URI and the second column its label. The format is the same as the extended subject file format for documents, specified below. For example:

```
<http://example.org/thesaurus/subj1>	networking
<http://example.org/thesaurus/subj2>	computer science
<http://example.org/thesaurus/subj3>	Internet Protocol
```

## Subject vocabulary as SKOS

A subject vocabulary can also be given as a SKOS/RDF file. All common RDF serializations (i.e. those supported by rdflib) are supported, including RDF/XML, Turtle and N-Triples.