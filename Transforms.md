Before a text is pre-processed by an [analyzer](https://github.com/NatLibFi/Annif/wiki/Analyzers) and passed to a backend the text can be modified by applying a transform to it. A transform can consist of multiple individual transforms that are applied sequentially. The specification for an individual transform is the name of a transform followed by possible arguments in parentheses, and multiple transforms can be given by separating them with commas. For example `transform=filter_lang,limit(5000)`.

## `limit` transform

Truncates an input document to a given length. It takes the number of characters to retain as parameter. 

This transform can be advantageous in case of long documents that have an abstract and/or introduction as it enables the backend to consider only those representative parts of the text. For example for [JYU theses](https://github.com/NatLibFi/Annif-corpora/tree/master/fulltext/jyu-theses) a good value for limit is 5000.

## `filter_lang` transform

Filters out sentences of the text whose language is different than the project language. It can take `text_min_length` and/or `sentence_min_length` keyword parameters. 

Generally, any text-parts in a "foreign" language are just useless or act as noise distracting analyzers and backends. When the limit transform is used the effect of these disadvantages can become more serious: for a document that has abstracts in two or even three languages (which is typical for theses) the retained text can mostly be in "foreign" languages.

The language detection is performed with [Compact Language Detector v3](https://github.com/google/cld3) via [`pycld3`](https://pypi.org/project/pycld3/). `pycld3` is an optional dependency of Annif, see the [installation page](https://github.com/NatLibFi/Annif/wiki/Optional-features-and-dependencies#language-filtering-with-pycld3). Detection and filtering is performed sentence-by-sentence. Because language detection for short sentences is unreliable, all sentences shorter than the `sentence_min_length` parameter value (in characters, by default 50) bypass the filtering. Also, if the whole text is shorter than `text_min_length` (by default 500) it bypasses the language filtering as one. 

When `filter_lang` is combined with the `limit` transform, for performance reasons it can be useful to apply an initial limit transform to avoid passing unnecessary amount of text to the language detection, for example with a transform setting like `transform=limit(15000),filter_lang,limit(5000)`.