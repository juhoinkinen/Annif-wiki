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

### Load all subjects from a directory

    annif load <projectid> <directory>

Parameters:
* `directory`: path to a directory containing text files in the corpus format

This will load all the subjects from the given directory in a single batch
operation. It is equivalent to executing `create-subject` on each file
separately.

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