The TF-IDF backend implements a baseline algorithm for automated subject indexing. The idea is to count the frequencies of terms (words) used in documents about each subject, use the [TF-IDF algorithm](https://en.wikipedia.org/wiki/Tf%E2%80%93idf) to weight the term frequencies so that rare words are more important than frequently occurring ones, and to create an index for matching term frequencies in new documents to those about specific subjects. The implementation is based on the topic modelling library [Gensim](https://radimrehurek.com/gensim/).

It is really easy to get started using the TF-IDF backend since it doesn't require any algorithm-specific configuration.

## Example configuration

```
[tfidf-en]
name=TF-IDF English
language=en
backends=tfidf
analyzer=snowball(english)
limit=100
vocab=yso-en
```

## Usage

Load a vocabulary:

    annif loadvoc tfidf-en /path/to/Annif-corpora/vocab/yso-en.tsv

Train the model:

    annif train tfidf-en /path/to/Annif-corpora/training/yso-finna-en.tsv.gz

Test the model with a single document:

    cat document.txt | annif analyze tfidf-en

Evaluate a directory full of files in fulltext [[document corpus|Document corpus formats]] format:

    annif eval tfidf-en /path/to/documents/