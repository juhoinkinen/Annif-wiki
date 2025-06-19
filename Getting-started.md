# Introduction

This is a very brief overview of how to get started using Annif. For more in-depth, hands-on materials, please take a look at the [Annif tutorial](https://github.com/NatLibFi/Annif-tutorial) - it provides introductory videos and exercises for learning how to use and apply Annif.

# Install Annif

First you need to install Annif. Follow the instructions in the top level [README](https://github.com/NatLibFi/Annif/blob/master/README.md) file.

# Define projects and backends

Projects and their backends can be defined in the `projects.cfg` file. By default Annif looks for this file in the current directory where it is executed, but the path can be overridden by using the `ANNIF_PROJECTS` environment variable or the `--projects` option after a command. The template file [`projects.cfg.dist`](https://github.com/NatLibFi/Annif/blob/master/projects.cfg.dist) already contains some projects, you can just copy it to `projects.cfg` to get started. See [Project configuration](https://github.com/NatLibFi/Annif/wiki/Project-configuration) for details and options for the projects configuration.

It's easiest to start with one of the predefined TF-IDF projects. If you use these, you will not need to touch the configuration files. Further down we will assume that you are using the `tfidf-en` project.

> [!NOTE]
> The `tfidf` backend is mostly meant to introduce Annif core features by allowing a quick setup; most probably other (Omikuji, MLLM) will give significantly better suggestions (even 25 %-points better F1@5 scores).

See [[Backends]] for a comparison of the features and requirements of all backends.

# Prepare and load a subject vocabulary

Annif stores vocabularies, models and other data files under a directory. This defaults to `data` under the current directory, but the path can be overridden by setting the `ANNIF_DATADIR` environment variable.

Most Annif backends require a subject vocabulary and some training data.

To get started, you can clone the [Annif-corpora](https://github.com/NatLibFi/Annif-corpora) repository which contains subject vocabularies and training documents in three languages created from Finna.fi metadata.

Of course you can also create your own vocabulary. The format is explained on the page [[Subject vocabulary formats]].

You now have to load the vocabulary that the project will use:

    annif load-vocab yso /path/to/Annif-corpora/vocab/yso-skos.ttl

This will take a few seconds.

Then you need some training data. We will train the model using the the English language training data generated from Finna.fi metadata (given in gzipped [short text corpus format](https://github.com/NatLibFi/Annif/wiki/Document-corpus-formats#short-text-document-corpus-tsv-file)):

    annif train tfidf-en /path/to/Annif-corpora/training/yso-finna-en.tsv.gz

This will take a few minutes. Now your Annif is ready for action!

# Test with an example document

You can test feeding a UTF-8-encoded text file into Annif like this:

    annif suggest tfidf-en document.txt

(NB. The command was called `analyze` before Annif 0.40)

After a while you should get a tab-separated list of subjects. This is a very inefficient way of using Annif since the model has to be loaded each time, which takes tens of seconds, but good for initial testing that everything works.

The `suggest` command also accepts multiple file paths or standard input as an argument.

# Evaluate with a directory full of files

If you have several documents with gold standard subjects, you can evaluate how well Annif works using the `eval` command. First you need to place the documents as text files in a directory and store the subjects in TSV files with the same basename. See [[Corpus formats]] for more information about the format. Then you can evaluate:

    annif eval tfidf-en /path/to/documents/

This will again take a while to start but then evaluation should not take a long time per document. In the end you will get a number of statistical measures, similar to this:

```
Precision (doc avg):    0.1714285714285714
Recall (doc avg):       0.36649659863945583
F1 score (doc avg):     0.2318510737628385
Precision (conc avg):   0.001245541364630077
Recall (conc avg):      0.0011485235510088807
F1 score (conc avg):    0.001146177490671795
Precision (microavg):   0.17142857142857143
Recall (microavg):      0.36363636363636365
F1 score (microavg):    0.23300970873786409
NDCG:                   0.36769238316041325
NDCG@5:                 0.3426718725322724
NDCG@10:                0.36769238316041325
Precision@1:            0.42857142857142855
Precision@3:            0.3571428571428571
Precision@5:            0.2857142857142857
LRAP:                   0.2467723479191469
True positives:         48
False positives:        232
False negatives:        84
Documents evaluated:    28
```

# Start up the application

You can run Annif as a web application that provides a REST API and a web UI for testing. This is much more efficient than using it from the command line since the heavy models are loaded just once. You can start Annif with:

    annif run

This will run a test server on http://localhost:5000/

The REST API is at http://localhost:5000/v1/

The Swagger UI documentation for the REST API is at http://localhost:5000/v1/ui/

# Next steps

Once you have one project/backend working you can start adding more projects and trying out more [[backends|Backends]]. See [[Project configuration]] for more information. Also, remember to check out the [Annif tutorial](https://github.com/NatLibFi/Annif-tutorial).

For production use you should try [[Running as a WSGI service]] and/or [[Usage with Docker]].

# Troubleshooting, questions and discussion

Check out the [[Annif-users -mailing list/forum | https://groups.google.com/forum/#!forum/annif-users]] and feel free to join the discussion or ask questions! Also see [[Annif-tutorial | https://github.com/NatLibFi/Annif-tutorial]] for comprehensive video instructions and exercises to help learn Annif features!

---

| [[↑ Wiki home|Home]] | [[System requirements →|System-requirements]]