Analyzers are used to pre-process, tokenize and normalize text. If you happen to be familiar with analyzers in Lucene, Solr and/or Elasticsearch, the concept is exactly the same although the details may differ a little bit. Analyzers are typically language-specific.

As of version 0.38, Annif supports three analyzers: `simple`, `snowball` and `voikko`. 

## `simple` analyzer

The `simple` analyzer only splits text into words and turns them all into lowercase.

## `snowball` analyzer

The `snowball` analyzer additionally performs stemming. It takes a language name as parameter, e.g. `snowball(english)` or `snowball(finnish)`. You can use any language supported by the NLTK Snowball stemmer; see the [NLTK stemmer documentation](http://www.nltk.org/howto/stem.html) for details on supported languages.
The supported languages as of NLTK 3.4.5 are:

    arabic danish dutch english finnish french german hungarian italian norwegian porter portuguese romanian russian spanish swedish

## `voikko` analyzer

The `voikko` analyzer performs lemmatization for Finnish. It takes a language code as parameter, e.g. `voikko(fi)`. This analyzer needs to be installed separately. See [[Optional features and dependencies]]