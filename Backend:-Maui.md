_**Note:** This page used to contain instructions on using MauiService. Nowadays the recommended method for integrating Annif with Maui is through Maui Server. The old MauiService instructions can still be found in the [history](https://github.com/NatLibFi/Annif/wiki/Backend:-Maui/324767fd4e47089f4d0964bdbe7be635c27c01ec) of this page. Unlike MauiService, Maui Server can be configured and trained directly through Annif using the normal `annif train` command, which makes it a lot easier to set up than MauiService._

The `maui` backend can be used to integrate Annif with [Maui Server](https://github.com/TopQuadrant/MauiServer), which is a RESTful microservice wrapper around the [Maui](http://www.medelyan.com/software) automated indexing tool. Maui was originally created by Alyona Medelyan and described in her PhD thesis ["Human-competitive automated topic indexing"](http://www.medelyan.com/files/phd2009.pdf?attredirects=0&d=1). We will use a forked, enhanced version of Maui and the microservice wrapper Maui Server that were developed by Spatineo Inc. for the National Library of Finland.

Maui is very good at detecting topics of text based on comparing terms in a controlled vocabulary to terms that appear in the document text. However, it cannot detect more abstract topics whose labels do not appear in text. For example, a topic such as "local history" would not be suggested for a document that describes the history of a village, unless that phrase is used in the document itself. Thus Maui works best when combined with another algorithm that relies on statistical associations.

Configuration of the `maui` backend is rather simple, but Maui Server has to be set up separately, either directly on the host system under Tomcat, or with Docker.

## Setting up Maui Server using Docker

You can start both Maui Server and Annif in Docker containers [using a compose file](https://github.com/NatLibFi/Annif/wiki/Usage-with-Docker#using-annif-with-gunicorn-nginx-and-maui-backend), or [just Maui Server in one container](https://github.com/NatLibFi/MauiServer/tree/dockerization#usage-with-docker).

## Setting up Maui Server using Tomcat

Maui is a Java application and Maui Server is a servlet designed to run within a servlet container such as Apache Tomcat, so you will need to install these first. On Ubuntu 16.04 and 18.04, you can install the Java environment and Tomcat like this:

    apt install tomcat8

The Maui and Maui Server versions developed at the National Library of Finland also support a [Voikko](https://voikko.puimula.org/)-based Finnish language "stemmer" (actually a lemmatizer) called `FinnishStemmer`, which relies on the `libvoikko` native library. It can be installed from Ubuntu packages:

    apt install libvoikko1 voikko-fi

### Installing Maui Server

The easiest way to install Maui Server is to download the pre-built packages from [Maven Central](https://search.maven.org/search?q=g:fi.nationallibrary). You should download the newest `mauiserver` WAR. The examples below assume that you are downloading them under the `/srv/maui` directory.

### Running Maui Server

First ensure that the Tomcat daemon has started up properly:

    service tomcat8 status

Create a data directory for Maui Server. We will use the path `/var/lib/mauidata`. You also need to give Tomcat permissions to read and write files within that directory:

    mkdir /var/lib/mauidata
    chown tomcat8:tomcat8 /var/lib/mauidata

Then edit the Tomcat configuration, setting the `MauiServer.dataDir` property to point to the path of the configuration file using the `-D` command line option. You probably also need to give Tomcat more memory (e.g. `-Xmx2G`), as the default is usually way too low. On Debian/Ubuntu systems, you need to edit `/etc/default/tomcat8` and change the `JAVA_OPTS` setting to something like this:

    JAVA_OPTS="-Djava.awt.headless=true -Xmx2G -XX:+UseConcMarkSweepGC -DMauiServer.dataDir=/var/lib/mauidata"

Then you will need to add the Maui Server servlet WAR to Tomcat. One easy way is to do this using a symlink, e.g.

    ln -s /srv/maui/mauiserver.war /var/lib/tomcat8/webapps/mauiserver.war

You probably don't want to include the version number in the webapp name, thus make sure to copy or symlink the WAR so it appears as `mauiserver.war` under the `webapps` directory.

Finally restart Tomcat:

    service tomcat8 restart

You can verify that Maui Server is working using curl:

    curl http://localhost:8080/mauiserver/

If everything is working, this should give you a JSON object with information like this:

    {"title":"Maui Server","data_dir":"/var/lib/mauidata","default_lang":"en","version":"1.3.2","taggers":[]}

If you get an error or other problem instead, check the Tomcat logs. The main one is `/var/log/tomcat8/logs/catalina.out`.

## Example configuration for Annif

Once you have MauiServer up and running, you can configure Annif to connect to it via the Maui backend. Here is an example:

```
[maui-en]
name=Maui English
language=en
backend=maui
endpoint=http://localhost:8080/mauiserver/
tagger=yso-en
vocab=yso-en
limit=1000
```

The parameters specific to Maui are `endpoint` and `tagger`. `endpoint` is the base URL where the Maui Server REST API can be accessed. `tagger` is an identifier for the tagger (combination of configuration, vocabulary and trained model) within Maui Server, very similar to a project within Annif. You can use the Annif project ID as the tagger ID. 

Note that if you are using [`docker-compose`](https://github.com/NatLibFi/Annif/wiki/Usage-with-Docker#using-annif-with-gunicorn-nginx-and-maui-backend), the `localhost` in the endpoint entries needs to be replaced with `mauiserver`.

## Usage

Load a vocabulary:

    annif loadvoc maui-en /path/to/Annif-corpora/vocab/yso-ysoplaces-skos-cicero.ttl

Note that Maui benefits a lot from having a full SKOS vocabulary with altLabels, hierarchical relations and related links. If you load a vocabulary from a TSV file, this information will not be available to Maui.

Train the Maui model:

    annif train maui-en /path/to/Annif-corpora/fulltext/jyu-theses/eng-validate/

Maui is best trained using a relatively small number (100-1000) of fulltext documents. When using a document corpus with a typical test/validate/train split, often the validate subset is good for this purpose.

Test the model with a single document:

    cat document.txt | annif suggest maui-en

Evaluate a directory full of files in fulltext [[document corpus|Document corpus formats]] format:

    annif eval maui-en /path/to/documents/

