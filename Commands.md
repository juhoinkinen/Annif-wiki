# Supported CLI commands in Annif

These are the command line commands of Annif, with REST API equivalents when
applicable.

Most of These methods take a `projectid` parameter. Projects are
identified by alphanumeric strings (`A-Za-z0-9_-`).

## Project administration

### List available projects

    annif list-projects

REST equivalent: 

    GET /projects/

Show a list of currently defined projects. Projects are defined in a
configuration file, normally called `projects.cfg`. See [[Project configuration]] for details.

### Show project information

    annif show-project <projectid>

REST equivalent:

    GET /projects/<projectid>

## Subject index administration

### Train a project on documents from directories or files

    annif train <projectid> <path> [<path2> ...]

Parameters:
* `path`: path(s) to a directory containing text files in the corpus format, or a TSV file (possibly gzipped)

This will train the project using all the documents from the given directory or TSV file in a single batch
operation.

REST equivalent: N/A

## Online learning

    annif learn <projectid> <path> [<path2> ...]

Parameters:
* `path`: path(s) to a directory containing text files in the corpus format, or a TSV file (possibly gzipped)

This will continue training an already trained project using all the documents from the given directory or TSV file in a single batch operation. Not supported by all backends.

REST equivalent: /projects/<projectid>/learn

## Automatic subject indexing

    annif analyze <projectid> [--limit=MAX] [--threshold=THRESHOLD] <document.txt

This will read a text document from standard input and suggest subjects for it.

Parameters:
* `limit`: maximum number of subjects to return
* `threshold`: minimum score threshold, below which results will not be returned

REST equivalent:

    POST /projects/<projectid>/analyze

## Evaluate on a collection of manually indexed files

    annif eval <projectid> [--limit=MAX] [--threshold=THRESHOLD] <path> [<path2> ...]

You need to supply the documents in one of the supported [[Document corpus formats]], i.e. either as a directory or as a TSV file. It is possible to give multiple corpora (even mixing corpus formats), in which case they will all be processed in the same run.

The output is a list of statistical measures.

Parameters:
* `limit`: maximum number of subjects to return
* `threshold`: minimum score threshold, below which results will not be returned
* `path`: path(s) to a directory containing text files in the corpus format or a TSV file (possibly gzipped)

REST equivalent: N/A

## Find the optimum limit and threshold for a directory of manually indexed files

    annif optimize <projectid> <path> [<path2> ...]

As with `eval`, you need to supply the documents in one of the supported [[Document corpus formats]].
This command will read each document, assign subjects to it using different limit and threshold values, and compare the results with the gold standard subjects. 

The output is a list of parameter combinations and their scores. From the output, you can determine the optimum limit and threshold parameters depending on which measure you want to target.

Parameters:
* `path`: path(s) to a directory containing text files in the corpus format or a TSV file (possibly gzipped)

REST equivalent: N/A

## Running a REST service

    annif run

This will start a development web server on http://localhost:5000/ .

REST equivalent: N/A