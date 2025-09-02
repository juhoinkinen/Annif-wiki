A document corpus (or several) is needed for training statistical or machine learning based models as well as for evaluating how well those models work. Annif supports two kinds document corpus formats: full text formats with each document in a separate file that is more suitable for longer documents (full text or long abstracts) and short text formats with many documents in a single file that is better suited for situations when you only have document titles and/or short descriptions or abstracts.

Here's an overview table summarizing the document corpus formats supported by Annif:

| Format Name                               | Type       | Subject URIs / Labels          | Supports Metadata | Supports `document_id` |
|-------------------------------------------|------------|--------------------------------|-------------------|------------------------|
| [TXT + TSV/KEY files](#txt--tsvkey-files) | Full text  | Both (TSV) / Labels only (KEY) | ❌ No             | ❌ No                  |
| [JSON files](#json-files)                 | Full text  | Both                           | ✅ Yes            | ✅ Yes                 |
| [TSV file](#tsv-file)                     | Short text | URIs only                      | ❌ No             | ❌ No                  |
| [CSV file](#csv-file)                     | Short text | URIs only                      | ✅ Yes            | ✅ Yes                 |
| [JSON Lines file](#json-lines-file)       | Short text | Both                           | ✅ Yes            | ✅ Yes                 |

**Notes:**

- **Full-text formats** store documents in separate files (in a directory) and are suitable for longer documents
- **Short text formats** store multiple documents in a single file and are ideal for titles/short descriptions
- **Subjects** can be expressed either as URIs or as labels, depending on format
- **Metadata** support varies by format, with JSON and CSV/JSON Lines supporting richer metadata
- **`document_id`** is supported in JSON and CSV/JSON Lines formats
- For TSV format, the simple .key format only supports labels, while the .tsv format supports both URIs and labels
- All formats use UTF-8 encoding

See also the [Annif-tutorial exercise with jupyter notebook for creating a custom corpus](https://github.com/NatLibFi/Annif-tutorial/blob/master/exercises/OPT_custom_corpus.md).

## Full-text document corpus formats

### TXT + TSV/KEY files

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

This is otherwise similar to the simple subject file format, but the subject file is now a UTF-8 encoded TSV (tab separated values) file with the file extension `.tsv`, where the first column contains a subject URI within angle brackets `<>` and the second column its label. For example:

```
<http://example.org/thesaurus/subj1>	networking
<http://example.org/thesaurus/subj2>	computer science
<http://example.org/thesaurus/subj3>	Internet Protocol
```

Any additional columns beyond the first two are ignored.

When using this format, subject comparison is performed based on URIs, not the labels. Since URIs are (or should be) more persistent than labels, this ensures that subjects can be matched even if the labels have changed in the subject vocabulary.

### JSON files

The JSON corpus format is a directory of files with the extension `.json`, each representing one document. The JSON structure for each file looks like this:

```json
{
  "document_id": "article1",
  "text": "Consider a future device ... in which an individual stores all his books, records, and communications...",
  "metadata": {
    "title": "As We May Think",
    "author": "Bush, Vannevar"
  },
  "subjects": [
    { "uri": "http://www.yso.fi/onto/yso/p817", "label": "future" },
    { "uri": "http://www.yso.fi/onto/yso/p3295", "label": "visions (prospects)" },
    { "uri": "http://www.yso.fi/onto/yso/p15527", "label": "science fiction" }
  ]
}
```

The `text` field contains the document text. The `metadata` field can be used to provide document metadata with arbitrary keys and values (strings). Either `text` or `metadata` is required; both can be provided. The `subjects` field is required when the corpus is used for e.g. `train` and `eval` operations that require subjects. Each given subject must have either an `uri` or `label` field (or both). An optional `document_id` field may be included. A [JSON Schema definition](https://github.com/NatLibFi/Annif/blob/main/annif/schemas/document.json) for validating this format is included in the Annif codebase.

## Short text document corpus formats

Short text formats are especially useful when only titles are known, or for very short documents. All documents are stored in a single file. Annif supports TSV, CSV and JSON Lines formats for short text corpora.

### TSV file

A document corpus is stored in a single UTF-8 encoded TSV file. There is no header row. The first column contains the text of the document (e.g. title or title + abstract) while the second column contains a whitespace-separated list of subject URIs (again within angle brackets) for that document. For example:

```
RFC 791: Internet Protocol	<http://example.org/thesaurus/subj1> <http://example.org/thesaurus/subj3>
RFC 1925: The Twelve Networking Truths	<http://example.org/thesaurus/subj1> <http://example.org/thesaurus/subj2>
Go To Statement Considered Harmful	<http://example.org/thesaurus/subj2>
```

Note that it is also possible to separate the subjects with tabs, thus creating a variable number of columns.

The TSV file may be compressed using gzip compression. A compressed file must have the extension `.gz`.

### CSV file

A document corpus is stored in a single UTF-8 encoded [CSV](https://en.wikipedia.org/wiki/Comma-separated_values) file, following typical CSV conventions: columns are separated by commas and values may optionally be enclosed in double quotes; some characters must be encoded by quoting. 

The first row is a header row defining the columns. Some column names are special:

* `text` is mandatory and contains the document text (e.g. title + abstract)
* `subject_uris` is mandatory for `train` and `eval` operations and contains the subject URIs, whitespace separated, within angle brackets
* `document_id` is an optional column reserved for document identifiers; these can be useful e.g. when using `annif index`, as its output (suggested subjects) contains references to document IDs
* all other column names are interpreted as document metadata; for example you can define columns for `title` and `publisher`

Example:

```
document_id,text,subject_uris
RFC791,"RFC 791: Internet Protocol","<http://example.org/thesaurus/subj1> <http://example.org/thesaurus/subj3>"
RFC1925,"RFC 1925: The Twelve Networking Truths","<http://example.org/thesaurus/subj1> <http://example.org/thesaurus/subj2>"
GoToHarmful,"Go To Statement Considered Harmful","<http://example.org/thesaurus/subj2>"
```

An uncompressed CSV file must have the extension `.csv`. It may be compressed using gzip compression. A compressed file must have the extension `.csv.gz`.

### JSON Lines file

A document corpus is stored in a single UTF-8 encoded [JSON Lines](https://jsonlines.org/) file. This is a JSON-derived format where each line in the file is a separate JSON record. The records use the same JSON structure as the JSON files fulltext format above. Example:

```json
{"text": "RFC 791: Internet Protocol", "subjects": [{"uri": "http://example.org/thesaurus/subj1"}, {"uri": "http://example.org/thesaurus/subj3"}]}
{"text": "RFC 1925: The Twelve Networking Truths", "subjects": [{"uri": "http://example.org/thesaurus/subj1"}, {"uri": "http://example.org/thesaurus/subj2"}]}
{"text": "Go To Statement Considered Harmful", "subjects": [{"uri": "http://example.org/thesaurus/subj2"}]}
```

An uncompressed JSON Lines file must have the extension `.jsonl`. It may be compressed using gzip compression. A compressed file must have the extension `.jsonl.gz`.


---
[[← System requirements|System-requirements]] | [[Project configuration →|Project-configuration]]
