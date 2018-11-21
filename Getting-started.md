# Install Annif

First you need to install Annif. Clone this repository and follow the instructions in the top level README file.

# Define projects and backends

Projects and their backends are defined in the `projects.cfg` file. By default Annif looks for this file in the current directory where it is executed, but the path can be overridden by setting the `ANNIF_PROJECTS` environment variable. The template file `[projects.cfg.dist](https://github.com/NatLibFi/Annif/blob/master/projects.cfg.dist)` already contains some projects, you can just copy it to `projects.cfg` to get started.

It's easiest to start with one of the predefined TF-IDF projects. If you use these, you will not need to touch the configuration files. Further down we will assume that you are using the `tfidf-en` project.

# Prepare and load a subject vocabulary

Annif stores vocabularies, models and other data files under a directory. This defaults to `data` under the current directory, but the path can be overridden by setting the `ANNIF_DATADIR` environment variable.

Most Annif backends require a subject vocabulary and some training data.

To get started, you can clone the [Annif-corpora](https://github.com/NatLibFi/Annif-corpora) repository which contains subject vocabularies and training documents in three languages created from Finna.fi metadata.

Of course you can also create your own vocabulary. The format is explained on the page [[Subject vocabulary formats]].

You now have to load the vocabulary that the project will use:

    annif loadvoc tfidf-en /path/to/Annif-corpora/vocab/yso-en.tsv

This will take a few seconds.

Then you need some training data. We will train the model using the the English language training data generated from Finna.fi metadata:

    annif train tfidf-en /path/to/Annif-corpora/training/yso-finna-en.tsv.gz

This will take a few minutes. Now your Annif is ready for action!

# Test with an example document

You can test by piping a UTF-8-encoded text file into Annif like this:

    cat document.txt | annif analyze tfidf-en

After a while you should get a tab-separated list of subjects. This is a very inefficient way of using Annif since the model has to be loaded each time, which takes tens of seconds, but good for initial testing that everything works.

# Evaluate with a directory full of files

If you have several documents with gold standard subjects, you can evaluate how well Annif works using the `evaldir` command. First you need to place the documents as text files in a directory and store the subjects in TSV files with the same basename. See [[Corpus formats]] for more information about the format. Then you can evaluate:

    annif eval tfidf-en /path/to/documents/

This will again take a while to start but then evaluation should not take a long time per document. In the end you will get a number of statistical measures.

# Start up the application

You can run Annif as a web application that provides a REST API and a web UI for testing. This is much more efficient than using it from the command line since the heavy models are loaded just once. You can start Annif with:

    annif run

This will run a test server on http://localhost:5000/

The REST API is at http://localhost:5000/v1/

The Swagger UI documentation for the REST API is at http://localhost:5000/v1/ui/

# Next steps

Once you have one project/backend working you can start adding more projects and trying out more backends. See [[Project configuration]] for more information.

For production use you should run Annif under a WSGI server (TBD).