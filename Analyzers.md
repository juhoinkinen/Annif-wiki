Analyzers are used to pre-process, tokenize and normalize text. If you happen to be familiar with analyzers in Lucene, Solr and/or Elasticsearch, the concept is exactly the same although the details may differ a little bit. Analyzers are typically language-specific.

As of version 0.38, Annif supports three analyzers: `simple`, `snowball` and `voikko`. 

## `simple` analyzer

The `simple` analyzer only splits text into words and turns them all into lowercase.

## `snowball` analyzer

The `snowball` analyzer additionally performs stemming. It takes a language name as parameter, e.g. `snowball(english)` or `snowball(finnish)`. You can use any language supported by the NLTK Snowball stemmer; see the [NLTK stemmer documentation](http://www.nltk.org/howto/stem.html) for details on supported languages.

## `voikko` analyzer

The `voikko` analyzer performs lemmatization for Finnish. It takes a language code as parameter, e.g. `voikko(fi)`. This analyzer needs to be installed separately. Assuming you are using Ubuntu, you fill first need to install the `libvoikko1` and `voikko-fi` packages:

    sudo apt install libvoikko1 voikko-fi

Then install the optional feature:

    pip install annif[voikko]