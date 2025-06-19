The `http` backend allows Annif to delegate subject suggestion requests to an external system via communicating it with REST API. The target system can be either another Annif instance or a compatible service that implements the necessary functionality:
a `suggest` method that accepts a document and returns a list of suggested subjects.

See for example [MauiServer](https://github.com/NatLibFi/MauiServer).

## Example configuration

```
[http-en]
name=HTTP English
language=en
backend=http
endpoint=http://localhost:5000/v1/projects/yso-en/suggest
vocab=yso
```

The `endpoint` setting specifies a URL where requests for subject suggestions are POSTed.

## Usage

Load a vocabulary:

    annif load-vocab yso /path/to/Annif-corpora/vocab/yso-skos.ttl

Training is not possible.

Test with a single document:

    cat document.txt | annif suggest http-en

Evaluate a directory full of files in fulltext [[document corpus|Document corpus formats]] format:

    annif eval http-en /path/to/documents/