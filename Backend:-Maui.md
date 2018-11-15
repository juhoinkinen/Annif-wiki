The HTTP backend can be used to access an external automated indexing service that provides an API similar to the Annif REST API. The main use of this backend is to integrate Annif with [MauiService](https://github.com/NatLibFi/mauiservice), which is a microservice wrapper around the [Maui](http://www.medelyan.com/software) automated indexing tool. Maui was originally created by Alyona Medelyan and described in her PhD thesis ["Human-competitive automated topic indexing"](http://www.medelyan.com/files/phd2009.pdf?attredirects=0&d=1). We will use a forked, enhanced version of Maui and the microservice wrapper MauiService that were developed by Spatineo Inc. for the National Library of Finland.

Maui is very good at detecting topics of text based on comparing terms in a controlled vocabulary to terms that appear in the document text. However, it cannot detect more abstract topics whose labels do not appear in text. For example, a topic such as "local history" would not be suggested for a document that describes the history of a village, unless that phrase is used in the document itself. Thus Maui works best when combined with another algorithm that relies on statistical associations.

Configuration of the `http` backend is rather simple, but MauiService has to be set up separately.


## Setting up MauiService

Maui is a Java application and MauiService is a servlet designed to run within a servlet container such as Apache Tomcat, so you will need to install these first. On Ubuntu 16.04 and 18.04, you can install the Java environment and Tomcat like this:

    apt install tomcat8

The Maui and MauiService versions developed at the National Library of Finland also support a [Voikko](https://voikko.puimula.org/)-based Finnish language "stemmer" (actually a lemmatizer) called `FinnishStemmer`, which relies on the `libvoikko` native library. It can be installed from a Ubuntu package:

    apt install libvoikko1

### Installing Maui and MauiService

The easiest way to install Maui and MauiService is to download the pre-built packages from [Maven Central](https://search.maven.org/search?q=g:fi.nationallibrary). You should download the newest `maui` JAR-with-dependencies and newest `mauiservice` WAR. The examples below assume that you are downloading them under the `/srv/maui` directory.

### Preparing a vocabulary for Maui

Maui requires the vocabulary to be in a SKOS file that uses RDF/XML syntax. As an example, YSO is available in this format from Finto.fi (always the current version) or from the [Annif-corpora repository](https://github.com/NatLibFi/Annif-corpora/tree/master/vocab) (a specific frozen version).

### Creating a model for Maui

Maui models are built from a small collection of indexed documents using the MauiModelBuilder class which can be executed from the command line. Some such collections are available in the [Annif-corpora repository](https://github.com/NatLibFi/Annif-corpora/tree/master/fulltext). We will use the `kirjastonhoitaja` (Ask a Librarian) collection for training Maui, specifically the `maui-train` subset of 200 documents.

You also need a SKOS vocabulary (see above) and some language-specific settings (language code, stemmer and stopwords). Here is an example for building a model from Finnish language documents. Note that building models can be quite memory-intensive; here we give the Java process 4GB of memory so it won't run out.

    java -Xmx4G -cp maui.jar com.entopix.maui.main.MauiModelBuilder -l ../Annif-corpora/fulltext/kirjastonhoitaja/maui-train/ -m kirjastonhoitaja -v ../Annif-corpora/vocab/yso-skos.rdf -f skos -i fi -s StopwordsFinnish -t CachingFinnishStemmer

Move the completed model (the file `jyu-fin`) under `/srv/maui`.

### Running MauiService

First ensure that the Tomcat daemon has started up properly:

    service tomcat8 status

Create a configuration file for MauiService called mauiservice.ini that looks like this, ensuring the paths to the model file and vocabulary match your setup:

```
[kirjastonhoitaja]
language = fi
model = /srv/maui/model/kirjastonhoitaja
stemmer = CachingFinnishStemmer
stopwords = StopwordsFinnish
vocab = /srv/Annif-corpora/vocab/yso-skos.rdf
vocabformat = skos
```

Then edit the Tomcat configuration, setting the `MAUISERVICE_CONFIGURATION` property to point to the path of the configuration file using the `-D` command line option. You probably also need to give Tomcat more memory (e.g. `-Xmx2G`), as the default is usually way too low. On Debian/Ubuntu systems, you need to edit `/etc/default/tomcat8` and change the `JAVA_OPTS` setting to something like this:

    JAVA_OPTS="-Djava.awt.headless=true -Xmx2G -XX:+UseConcMarkSweepGC -DMAUISERVICE_CONFIGURATION=/srv/maui/mauiservice.ini"

Then you will need to add the MauiService servlet WAR to Tomcat. One easy way is to do this using a symlink, e.g.

    ln -s /srv/maui/mauiservice.war /var/lib/tomcat8/webapps/mauiservice.war

You probably don't want to include the version number to the webapp name, thus make sure to copy or symlink the WAR so it appears as `mauiservice.war` under the `webapps` directory.

Finally restart Tomcat:

    service tomcat8 restart

You can verify that MauiService is working using curl:

    curl http://localhost:8080/mauiservice/

If everything is working, this should give you a JSON list of configured projects, like this:

    ["kirjastonhoitaja"]

If you get an error or other problem instead, check the Tomcat logs. The main one is `/var/log/tomcat8/logs/catalina.out`.

## Example configuration for Annif

Once you have MauiService up and running, you can configure Annif to connect to it via the HTTP backend. Here is an example:

```
[maui-en]
name=Maui English
language=en
backends=http
endpoint=http://localhost:8080/mauiservice/jyu-eng/analyze
vocab=yso-en
```

## Usage

Load a vocabulary:

    annif loadvoc maui-en /path/to/Annif-corpora/vocab/yso-en.tsv

Training the model on the Annif side is not necessary or even possible.

Test the model with a single document:

    cat document.txt | annif analyze maui-en

Evaluate a directory full of files in fulltext [[document corpus|Document corpus formats]] format:

    annif eval maui-en /path/to/documents/