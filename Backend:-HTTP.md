The HTTP backend communicates with a REST API that provides an `analyze` method. It can be either another instance of Annif or a service such as MauiService (see [[Backend: Maui]] for more details about using MauiService).

## Example configuration

```
[http-en]
name=HTTP English
language=en
backends=http
endpoint=http://localhost:5050/yso-en/analyze
vocab=yso-en
```

The `endpoint` setting specifies a URL where analysis requests are POSTed.

## Usage

Load a vocabulary:

    annif loadvoc http-en /path/to/Annif-corpora/vocab/yso-en.tsv

Training is not possible.

Test with a single document:

    cat document.txt | annif analyze http-en

Evaluate a directory full of files in fulltext [[document corpus|Document corpus formats]] format:

    annif eval http-en /path/to/documents/