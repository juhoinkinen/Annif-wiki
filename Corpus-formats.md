# Corpus formats

Annif uses different kinds of corpora. This document specifies the formats.

## Subject vocabulary formats

A subject vocabulary defines the subjects available for automated indexing or classification. Typically this will be a thesaurus, a classification or a list of subject headings. Annif doesn't care much about the internal structure of a subject vocabulary, it just needs to know the URIs and preferred labels (a.k.a. terms or descriptors) of each subject/class/concept.

### Subject vocabulary as TSV

The simple TSV subject vocabulary format only specifies URIs and labels for concepts. The vocabulary file file is UTF-8 encoded TSV (tab separated values) file with the file extension `.tsv`, where the first column contains a subject URI and the second column its label. The format is the same as the extended subject file format for documents, specified below. For example:

```
<http://example.org/thesaurus/subj1>	networking
<http://example.org/thesaurus/subj2>	computer science
<http://example.org/thesaurus/subj3>	Internet Protocol
```

### Subject vocabulary as SKOS

A subject vocabulary can also be given as a SKOS/RDF file. All common RDF serializations (i.e. those supported by rdflib) are supported, including RDF/XML, Turtle and N-Triples.

## Document corpus

A document corpus (or several) is needed for training statistical or machine learning based models as well as for evaluating how well those models work. Annif supports two document corpus formats: one that is more suitable for longer documents (full text or long abstracts) and another that is better suited for short texts such as when you only have document titles, not their full texts.

### Full-text document corpus (directory)

The full text corpus is a directory with UTF-8 encoded text files that have the file extension `.txt`.

The directory may also contain **subject files** that list the assigned subjects for each file. The file name is the same as the document file, but with the file extension `.key` (simple format) or `.tsv` (extended format). For example, `document1.txt` may have a corresponding subject file `document1.key`. Subject files come in two
formats:

#### Simple subject file format

This file lists subject labels, UTF-8 encoded, one per line. For example:

```
networking
computer science
Internet Protocol
```

Note that the labels must exactly match the preferred labels of concepts in the subject vocabulary.

This format corresponds to the [Maui topic file format](https://code.google.com/archive/p/maui-indexer/wikis/Usage.wiki).

#### Extended subject file format

This is otherwise similar to the simple subject file format, but the subject file is now a UTF-8 encoded TSV (tab separated values) file with the file extension `.tsv`, where the first column contains a subject URI and the second column its label. For example:

```
<http://example.org/thesaurus/subj1>	networking
<http://example.org/thesaurus/subj2>	computer science
<http://example.org/thesaurus/subj3>	Internet Protocol
```

Any additional columns beyond the first two are ignored.

When using this format, subject comparison is performed based on URIs, not the labels. Since URIs are (or should be) more persistent than labels, this ensures that subjects can be matched even if the labels have changed in the subject
vocabulary.

### Short text document corpus (TSV file)

A document corpus can be given in a single UTF-8 encoded TSV file. This format is especially useful for metadata about documents, when only titles are known, or for very short documents. The first column contains the text of the document (e.g. title or title + abstract) while the second column contains a whitespace-separated list of subject URIs for that document. For example:

```
RFC 791: Internet Protocol	<http://example.org/thesaurus/subj1> <http://example.org/thesaurus/subj3>
RFC 1925: The Twelve Networking Truths	<http://example.org/thesaurus/subj1> <http://example.org/thesaurus/subj2>
Go To Statement Considered Harmful	<http://example.org/thesaurus/subj2>
```

Note that it is also possible to separate the subjects with tabs, thus creating a variable number of columns.