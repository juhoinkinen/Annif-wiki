# Supported CLI commands in Annif

These are the command line commands of Annif, with REST API equivalents when
applicable.

Most of these methods take a `projectid` parameter. Projects are
identified by alphanumeric strings (`A-Za-z0-9_-`).

## Project administration

### Load vocabulary

    annif loadvoc <projectid> <subjectfile>

Parameters:
* `subjectfile`: path to a file containing subjects in [a subject vocabulary format](https://github.com/NatLibFi/Annif/wiki/Subject-vocabulary-formats)

This will load the vocabulary to be used in subject indexing. Note that although `projectid` is a parameter of the command, the vocabulary is shared by all the projects with the same `vocab` identifier in [the project configuration](https://github.com/NatLibFi/Annif/wiki/Project-configuration), and the vocabulary only needs to be loaded for one of those projects. If a vocabulary has already been loaded, reinvoking `loadvoc` with a new subject file will update the Annif's internal vocabulary: label names are updated and any subject not appearing in the new subject file is removed. Note that new subjects won't be suggested before the project is retrained with the updated vocabulary.


REST equivalent: N/A

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

### Clear project

    annif clear <projectid>

Initialize a project to its original, untrained state: removes the data files of the model. 

REST equivalent: N/A

## Subject index administration

### Train a project on documents from directories or files

    annif train <projectid> <path> [<path2> ...] [--projects FILE] [--backend-param BACKEND.PARAM=VAL]

or

    annif train <projectid> --cached [--projects FILE] [--backend-param BACKEND.PARAM=VAL]


Parameters:
* `path`: path(s) to a directory containing text files in the corpus format, or a TSV file (possibly gzipped)
* `projects`: Set path to config file
* `backend-param`: Override a backend parameter of the config file
* `docs-limit`: Maximum number of documents to use
* `jobs`: Number of threads/CPUs to use in parallel (not supported by all backends)
* `cached`: If set, reuse preprocessed training data from the previous run. See [[Reusing preprocessed training data]]

This will train the project using all the documents from the given directory or TSV file in a single batch
operation.

REST equivalent: N/A

## Online learning

    annif learn <projectid> <path> [<path2> ...] [--projects FILE] [--backend-param BACKEND.PARAM=VAL]

Parameters:
* `path`: path(s) to a directory containing text files in the corpus format, or a TSV file (possibly gzipped)
* `projects`: Set path to config file
* `backend-param`: Override a backend parameter of the config file
* `docs-limit`: Maximum number of documents to use

This will continue training an already trained project using all the documents from the given directory or TSV file in a single batch operation. Not supported by all backends.

REST equivalent: /projects/<projectid>/learn

## Automatic subject indexing

    annif suggest <projectid> [--limit MAX] [--threshold THRESHOLD] [--projects FILE] [--backend-param BACKEND.PARAM=VAL] <document.txt

This will read a text document from standard input and suggest subjects for it.

Parameters:
* `limit`: maximum number of subjects to return
* `threshold`: minimum score threshold, below which results will not be returned
* `projects`: Set path to projects.cfg
* `backend-param`: Override a backend parameter of the config file

REST equivalent:

    POST /projects/<projectid>/suggest

## Evaluate on a collection of manually indexed files

    annif eval <projectid> [--limit MAX] [--threshold THRESHOLD] [--projects FILE] <path> [<path2> ...]

You need to supply the documents in one of the supported [[Document corpus formats]], i.e. either as a directory or as a TSV file. It is possible to give multiple corpora (even mixing corpus formats), in which case they will all be processed in the same run.

The output is a list of statistical measures.

Parameters:
* `limit`: maximum number of subjects to return
* `threshold`: minimum score threshold, below which results will not be returned
* `results-file`: Specify file in order to write non-aggregated results per subject. File directory must exists, existing file will be overwritten.
* `jobs`: Number of parallel jobs (0 means all CPUs)
* `backend-param`: Override a backend parameter of the config file
* `docs-limit`: Maximum number of documents to use
* `projects`: Set path to projects.cfg

REST equivalent: N/A

## Find the optimum limit and threshold for a directory of manually indexed files

    annif optimize <projectid> <path> [--projects FILE] [--backend-param BACKEND.PARAM=VAL] [<path2> ...]

As with `eval`, you need to supply the documents in one of the supported [[Document corpus formats]].
This command will read each document, assign subjects to it using different limit and threshold values, and compare the results with the gold standard subjects. 

The output is a list of parameter combinations and their scores. From the output, you can determine the optimum limit and threshold parameters depending on which measure you want to target.

Parameters:
* `path`: path(s) to a directory containing text files in the corpus format or a TSV file (possibly gzipped)
* `projects`: Set path to projects.cfg
* `backend-param`: Override a backend parameter of the config file
* `docs-limit`: Maximum number of documents to use

REST equivalent: N/A

## Running a REST service

    annif run

This will start a development web server on http://localhost:5000/ .

REST equivalent: N/A