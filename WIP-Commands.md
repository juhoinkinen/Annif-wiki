# Supported CLI commands in Annif

These are the command line commands of Annif, with REST API equivalents when applicable.

Most of these methods take a projectid parameter. Projects are identified by alphanumeric strings `(A-Za-z0-9_-)`.

## Project administration

### annif loadvoc

Load a vocabulary for a project.

```shell
annif loadvoc [OPTIONS] PROJECT_ID SUBJECTFILE
```

#### Options


###### -f, --force
Replace existing vocabulary completely instead of updating it


###### -v, --verbosity <LVL>
Either CRITICAL, ERROR, WARNING, INFO or DEBUG


###### -p, --projects <projects>
Set path to project configuration file or directory

#### Arguments


###### PROJECT_ID
Required argument


###### SUBJECTFILE
Required argument

This will load the vocabulary to be used in subject indexing. Note that although `PROJECT_ID` is a parameter of the command, the vocabulary is shared by all the projects with the same vocab identifier in the project configuration, and the vocabulary only needs to be loaded for one of those projects.

If a vocabulary has already been loaded, reinvoking `loadvoc` with a new subject file will update the Annif’s internal vocabulary: label names are updated and any subject not appearing in the new subject file is removed. Note that new subjects will not be suggested before the project is retrained with the updated vocabulary. The update behavior can be overridden with the `--force` option.

REST equivalent: N/A

### annif list-projects

List available projects.

```shell
annif list-projects [OPTIONS]
```

#### Options


###### -v, --verbosity <LVL>
Either CRITICAL, ERROR, WARNING, INFO or DEBUG


###### -p, --projects <projects>
Set path to project configuration file or directory


###### -v, --verbosity <LVL>
Either CRITICAL, ERROR, WARNING, INFO or DEBUG

Show a list of currently defined projects. Projects are defined in a configuration file, normally called projects.cfg. See Project configuration for details.

REST equivalent:

```default
GET /projects/
```

### annif show-project

Show information about a project.

```shell
annif show-project [OPTIONS] PROJECT_ID
```

#### Options


###### -v, --verbosity <LVL>
Either CRITICAL, ERROR, WARNING, INFO or DEBUG


###### -p, --projects <projects>
Set path to project configuration file or directory

#### Arguments


###### PROJECT_ID
Required argument

REST equivalent::

```default
GET /projects/<projectid>
```

### annif clear-project

Initialize the project to its original, untrained state.

```shell
annif clear-project [OPTIONS] PROJECT_ID
```

#### Options


###### -v, --verbosity <LVL>
Either CRITICAL, ERROR, WARNING, INFO or DEBUG


###### -p, --projects <projects>
Set path to project configuration file or directory

#### Arguments


###### PROJECT_ID
Required argument

REST equivalent: N/A

## Subject index administration

### annif train

Train a project on a collection of documents.

```shell
annif train [OPTIONS] PROJECT_ID [PATHS]...
```

#### Options


###### -c, --cached, -C, --no-cached
Reuse preprocessed training data from previous run


###### -d, --docs-limit <docs_limit>
Maximum number of documents to use


###### -j, --jobs <jobs>
Number of parallel jobs (0 means choose automatically)


###### -b, --backend-param <backend_param>
Override backend parameter of the config file. Syntax: “-b <backend>.<parameter>=<value>”.


###### -v, --verbosity <LVL>
Either CRITICAL, ERROR, WARNING, INFO or DEBUG


###### -p, --projects <projects>
Set path to project configuration file or directory

#### Arguments


###### PROJECT_ID
Required argument


###### PATHS
Optional argument(s)

This will train the project using all the documents from the given directory or TSV file in a single batch operation.

REST equivalent: N/A

### annif learn

Further train an existing project on a collection of documents.

```shell
annif learn [OPTIONS] PROJECT_ID [PATHS]...
```

#### Options


###### -d, --docs-limit <docs_limit>
Maximum number of documents to use


###### -b, --backend-param <backend_param>
Override backend parameter of the config file. Syntax: “-b <backend>.<parameter>=<value>”.


###### -v, --verbosity <LVL>
Either CRITICAL, ERROR, WARNING, INFO or DEBUG


###### -p, --projects <projects>
Set path to project configuration file or directory

#### Arguments


###### PROJECT_ID
Required argument


###### PATHS
Optional argument(s)

This will continue training an already trained project using all the documents from the given directory or TSV file in a single batch operation. Not supported by all backends.

REST equivalent:

```default
POST /projects/<projectid>/learn
```

### annif suggest

Suggest subjects for a single document from standard input.

```shell
annif suggest [OPTIONS] PROJECT_ID
```

#### Options


###### -l, --limit <limit>
Maximum number of subjects


###### -t, --threshold <threshold>
Minimum score threshold


###### -b, --backend-param <backend_param>
Override backend parameter of the config file. Syntax: “-b <backend>.<parameter>=<value>”.


###### -v, --verbosity <LVL>
Either CRITICAL, ERROR, WARNING, INFO or DEBUG


###### -p, --projects <projects>
Set path to project configuration file or directory

#### Arguments


###### PROJECT_ID
Required argument

This will read a text document from standard input and suggest subjects for it.

REST equivalent:

```default
POST /projects/<projectid>/suggest
```

### annif eval

Analyze documents and evaluate the result.

Compare the results of automated indexing against a gold standard. The
path may be either a TSV file with short documents or a directory with
documents in separate files.

```shell
annif eval [OPTIONS] PROJECT_ID [PATHS]...
```

#### Options


###### -l, --limit <limit>
Maximum number of subjects


###### -t, --threshold <threshold>
Minimum score threshold


###### -d, --docs-limit <docs_limit>
Maximum number of documents to use


###### -m, --metric <metric>
Metric to calculate (default: all)


###### -M, --metrics-file <metrics_file>
Specify file in order to write evaluation metrics in JSON format.
File directory must exist, existing file will be overwritten.


###### -r, --results-file <results_file>
Specify file in order to write non-aggregated results per subject.
File directory must exist, existing file will be overwritten.


###### -j, --jobs <jobs>
Number of parallel jobs (0 means all CPUs)


###### -b, --backend-param <backend_param>
Override backend parameter of the config file. Syntax: “-b <backend>.<parameter>=<value>”.


###### -v, --verbosity <LVL>
Either CRITICAL, ERROR, WARNING, INFO or DEBUG


###### -p, --projects <projects>
Set path to project configuration file or directory

#### Arguments


###### PROJECT_ID
Required argument


###### PATHS
Optional argument(s)

You need to supply the documents in one of the supported Document corpus formats, i.e. either as a directory or as a TSV file. It is possible to give multiple corpora (even mixing corpus formats), in which case they will all be processed in the same run.

The output is a list of statistical measures.

REST equivalent: N/A

### annif optimize

Analyze documents, testing multiple limits and thresholds.

Evaluate the analysis results for a directory with documents against a
gold standard given in subject files. Test different limit/threshold
values and report the precision, recall and F-measure of each combination
of settings.

```shell
annif optimize [OPTIONS] PROJECT_ID [PATHS]...
```

#### Options


###### -d, --docs-limit <docs_limit>
Maximum number of documents to use


###### -b, --backend-param <backend_param>
Override backend parameter of the config file. Syntax: “-b <backend>.<parameter>=<value>”.


###### -v, --verbosity <LVL>
Either CRITICAL, ERROR, WARNING, INFO or DEBUG


###### -p, --projects <projects>
Set path to project configuration file or directory

#### Arguments


###### PROJECT_ID
Required argument


###### PATHS
Optional argument(s)

As with eval, you need to supply the documents in one of the supported Document corpus formats. This command will read each document, assign subjects to it using different limit and threshold values, and compare the results with the gold standard subjects.

The output is a list of parameter combinations and their scores. From the output, you can determine the optimum limit and threshold parameters depending on which measure you want to target.

REST equivalent: N/A

### annif index

Index a directory with documents, suggesting subjects for each document.
Write the results in TSV files with the given suffix.

```shell
annif index [OPTIONS] PROJECT_ID DIRECTORY
```

#### Options


###### -s, --suffix <suffix>
File name suffix for result files


###### -f, --force, -F, --no-force
Force overwriting of existing result files


###### -l, --limit <limit>
Maximum number of subjects


###### -t, --threshold <threshold>
Minimum score threshold


###### -b, --backend-param <backend_param>
Override backend parameter of the config file. Syntax: “-b <backend>.<parameter>=<value>”.


###### -v, --verbosity <LVL>
Either CRITICAL, ERROR, WARNING, INFO or DEBUG


###### -p, --projects <projects>
Set path to project configuration file or directory

#### Arguments


###### PROJECT_ID
Required argument


###### DIRECTORY
Required argument

### annif run

Run a local development server.

This server is for development purposes only. It does not provide
the stability, security, or performance of production WSGI servers.

The reloader and debugger are enabled by default with the ‘–debug’
option.

```shell
annif run [OPTIONS]
```

#### Options


###### -h, --host <host>
The interface to bind to.


###### -p, --port <port>
The port to bind to.


###### --cert <cert>
Specify a certificate file to use HTTPS.


###### --key <key>
The key file to use when specifying a certificate.


###### --reload, --no-reload
Enable or disable the reloader. By default the reloader is active if debug is enabled.


###### --debugger, --no-debugger
Enable or disable the debugger. By default the debugger is active if debug is enabled.


###### --with-threads, --without-threads
Enable or disable multithreading.


###### --extra-files <extra_files>
Extra files that trigger a reload on change. Multiple paths are separated by ‘:’.


###### --exclude-patterns <exclude_patterns>
Files matching these fnmatch patterns will not trigger a reload on change. Multiple patterns are separated by ‘:’.
