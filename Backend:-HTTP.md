The `http` backend communicates with a REST API that provides a `suggest` method. It can be either another instance of Annif or a service such as [MauiServer](https://github.com/NatLibFi/MauiServer).

## Example configuration

```
[http-en]
name=HTTP English
language=en
backend=http
endpoint=http://localhost:5050/yso-en/suggest
vocab=yso
```

The `endpoint` setting specifies a URL where requests for subject suggestions are POSTed.

## Usage

Load a vocabulary:

    annif loadvoc http-en /path/to/Annif-corpora/vocab/yso-en.tsv

Training is not possible.

Test with a single document:

    cat document.txt | annif suggest http-en

Evaluate a directory full of files in fulltext [[document corpus|Document corpus formats]] format:

    annif eval http-en /path/to/documents/