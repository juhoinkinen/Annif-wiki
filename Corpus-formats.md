# Corpus formats

Annif uses different kinds of corpora. This document specifies the formats.

## Full-text document corpus

The full text corpus is a directory with UTF-8 encoded text files that have
the file extension `.txt`.

The directory may also contain **subject files** that list the assigned
subjects for each file. The file name is the same as the document file, but
with the file extension `.key`. For example, `document1.txt` may have a
corresponding subject file `document1.key`. Subject files come in two
formats:

### Simple subject file format

This file lists subject labels, UTF-8 encoded, one per line. For example:

```
networking
computer science
Internet Protocol
```

Note that the labels must exactly match the preferred labels of concepts in
the subject vocabulary.

This format corresponds to the [Maui topic file
format](https://code.google.com/archive/p/maui-indexer/wikis/Usage.wiki).

### Extended subject file format

This is otherwise similar to the simple subject file format, but the subject
file is now a UTF-8 encoded TSV (tab separated values) file with the file 
extension `.tsv`, where the first column contains a subject URI and the second 
column its label. For example:

```
<http://example.org/thesaurus/subj1>	networking
<http://example.org/thesaurus/subj2>	computer science
<http://example.org/thesaurus/subj3>	Internet Protocol
```

Any additional columns beyond the first two are ignored.

When using this format, subject comparison is performed based on URIs, not
the labels. Since URIs are more persistent than labels, this ensures that
subjects can be matched even if the labels have changed in the subject
vocabulary.

## Subject corpus

A subject corpus lists the available subjects (typically defined in a controlled vocabulary) together with text that is representative of that subject. The text may be gathered e.g. from metadata records. A subject corpus is represented as a directory with UTF-8 encoded text files with the extension `.txt`. Each file has the following structure:

* The first line contains the URI and label of the subject, separated by a single space
* Starting with the second line, there is some text related to the subject. It is recommended that the text starts with the label of the concept itself on the first line (second line of the file) and further lines are each based on a single document.

Here is an example from the YSO/Finna.fi corpus:

```
http://www.yso.fi/onto/yso/p10088 municipal boards
municipal boards
Content of municipal strategy papers : comparison of five Finnish and five Canadian municipalities' strategies
The Swedish local government act
When municipalities lead co-production : Lessons from a Danish case study
```

## Metadata only corpus

TBD