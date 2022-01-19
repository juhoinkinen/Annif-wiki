Analyzers are used to pre-process, tokenize and normalize text. If you happen to be familiar with analyzers in Lucene, Solr and/or Elasticsearch, the concept is exactly the same although the details may differ a little bit. Analyzers are typically language-specific.

By default the tokenization discards all words that are shorter than three characters, but this can be configured by setting `token_min_length` in the analyzer parameters. For example, to discard only words of one character (when using the `snowball` analyzer for English), use `snowball(english,token_min_length=2)`.

Annif supports four analyzers: `simple`, `snowball`, `voikko` and `spacy`. 

## `simple` analyzer

The `simple` analyzer only splits text into words and turns them all into lowercase.

## `snowball` analyzer

The `snowball` analyzer additionally performs stemming. It takes a language name as parameter, e.g. `snowball(english)` or `snowball(finnish)`. You can use any language supported by the NLTK Snowball stemmer; see the [NLTK stemmer documentation](http://www.nltk.org/howto/stem.html) for details on supported languages.
The supported languages as of NLTK 3.4.5 are:

    arabic danish dutch english finnish french german hungarian italian norwegian porter portuguese romanian russian spanish swedish

## `voikko` analyzer

The `voikko` analyzer performs lemmatization for Finnish. It takes a language code as parameter, e.g. `voikko(fi)`. This analyzer needs to be installed separately. See [[Optional features and dependencies]]

## `spacy` analyzer

The `spacy` analyzer performs lemmatization for many languages using the [spaCy NLP toolkit](https://spacy.io/). See [Models & Languages](https://spacy.io/usage/models) for the current list of supported languages. 

The analyzer takes a language code as parameter, e.g. `spacy(en_core_web_sm)`. Optionally, lemmas can be forced to lowercase using the `lowercase` option, like this: `spacy(en_core_web_sm,lowercase=1)`

This analyzer and the language-specific models need to be installed separately. See [[Optional features and dependencies]]