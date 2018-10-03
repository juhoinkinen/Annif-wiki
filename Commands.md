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
configuration file, normally called `projects.cfg`.

### Show project information

    annif show-project <projectid>

REST equivalent:

    GET /projects/<projectid>

## Subject index administration

### Train a project on documents from directories or files

    annif train <projectid> <path> [<path2> ...]

Parameters:
* `path`: path(s) to a directory containing text files in the corpus format or a TSV file (possibly gzipped)

This will train the project using all the documents from the given directory or TSV file in a single batch
operation.

REST equivalent: N/A

## Automatic subject indexing

    annif analyze <projectid> [--limit=MAX] [--threshold=THRESHOLD] <document.txt

This will read a text document from standard input and suggest subjects for
it.

Parameters:
* `limit`: maximum number of subjects to return
* `threshold`: minimum score threshold, below which results will not be returned

REST equivalent:

    POST /projects/<projectid>/analyze

## Evaluate on a directory of manually indexed files

    annif eval <projectid> [--limit=MAX] [--threshold=THRESHOLD] <path> [<path2> ...]

First you need to place the documents as text files in a directory and store the subjects in TSV files with the same basename. This command will read each .txt file from the directory, assign subjects to it, and compare them with the gold standard subjects given in the corresponding .tsv file. The output is a list of statistical measures.

Parameters:
* `limit`: maximum number of subjects to return
* `threshold`: minimum score threshold, below which results will not be returned
* `path`: path(s) to a directory containing text files in the corpus format or a TSV file (possibly gzipped)

REST equivalent: N/A

## Find the optimum limit and threshold for a directory of manually indexed files

    annif optimize <projectid> <path> [<path2> ...]

As with `evaldir`, you need to place the documents as text files in a directory and store the subjects in TSV files with the same basename. This command will read each .txt file from the directory, assign subjects to it using different limit and threshold values, and compare the results with the gold standard subjects given in the corresponding .tsv file. The output is a list of parameter combinations and their scores.

Parameters:
* `path`: path(s) to a directory containing text files in the corpus format or a TSV file (possibly gzipped)

REST equivalent: N/A

## Running a REST service

    annif run

This will start a development web server on http://localhost:5000/ .

REST equivalent: N/A