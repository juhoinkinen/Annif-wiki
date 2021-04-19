_**Note:** 
As Maui needs to be run as a separate service (either via MauiServer or MauiService), it can be somewhat inconvenient to use. Since Annif 0.52, instead of Maui you can use the [MLLM backend](https://github.com/NatLibFi/Annif/wiki/Backend%3A-MLLM). MLLM is based on many ideas that are used in the Maui algorithm, but is implemented as an internal module in Annif, and it is recommended to use MLLM rather than Maui._

_This page used to contain instructions on using MauiService. Nowadays the recommended method for integrating Annif with Maui is through Maui Server. The old MauiService instructions can still be found in the [history](https://github.com/NatLibFi/Annif/wiki/Backend:-Maui/324767fd4e47089f4d0964bdbe7be635c27c01ec) of this page. Unlike MauiService, Maui Server can be configured and trained directly through Annif using the normal `annif train` command, which makes it a lot easier to set up than MauiService._

The `maui` backend can be used to integrate Annif with [Maui Server](https://github.com/TopQuadrant/MauiServer), which is a RESTful microservice wrapper, built by TopQuadrant Inc., around the [Maui](http://www.medelyan.com/software) automated indexing tool. Maui was originally created by Alyona Medelyan and described in her PhD thesis ["Human-competitive automated topic indexing"](https://hdl.handle.net/10289/3513). We will use a forked, enhanced version of Maui and the microservice wrapper Maui Server that were developed by the National Library of Finland in collaboration with Spatineo Inc.

Maui is very good at detecting topics of text based on comparing terms in a controlled vocabulary to terms that appear in the document text. However, it cannot detect more abstract topics whose labels do not appear in text. For example, a topic such as "local history" would not be suggested for a document that describes the history of a village, unless that phrase is used in the document itself. Thus Maui works best when combined with another algorithm that relies on statistical associations.

Configuration of the `maui` backend is rather simple, but Maui Server has to be set up separately, either directly on the host system under Tomcat, or with Docker.

## Setting up Maui Server using Docker

You can start both Maui Server and Annif in Docker containers [using a compose file](https://github.com/NatLibFi/Annif/wiki/Usage-with-Docker#using-annif-with-gunicorn-nginx-and-maui-backend), or [just Maui Server in one container](https://github.com/NatLibFi/MauiServer/tree/dockerization#usage-with-docker).

## Setting up Maui Server using Tomcat

### Downloading Maui Server

First you need to download the pre-built Web Application Resource package from [Maven Central](https://search.maven.org/search?q=g:fi.nationallibrary). You should download the newest `mauiserver` WAR.

### Installing Tomcat (and Voikko libraries)

Maui is a Java application and Maui Server is a servlet designed to run within a servlet container such as Apache Tomcat, so you will need to install these first. On Ubuntu 18.04 and 20.04, you can install the Java environment and Tomcat version 9 like this:

    apt install tomcat9

The Maui and Maui Server versions developed at the National Library of Finland also support a [Voikko](https://voikko.puimula.org/)-based Finnish language "stemmer" (actually a lemmatizer) called `FinnishStemmer`, which relies on the `libvoikko` native library. If you need the Finnish language support for Maui, the Voikko libraries can be installed from Ubuntu packages:

    apt install libvoikko1 voikko-fi

Now ensure that the Tomcat daemon has started up properly:

    service tomcat9 status

If it says `active (running)` then Tomcat has been successfully installed.

### Configuring Tomcat for Maui Server

First create a data directory for Maui Server. We will use the path `/var/lib/mauidata`. You also need to give Tomcat permissions to read and write files within that directory:

    mkdir /var/lib/mauidata
    chown tomcat:tomcat /var/lib/mauidata

Then edit the Tomcat configuration, setting the `MauiServer.dataDir` property to point to the path of the configuration file using the `-D` command line option. You probably also need to give Tomcat more memory (e.g. `-Xmx2G`), as the default is often way too low. On Debian/Ubuntu systems, you need to edit `/etc/default/tomcat9` and add those options into the `JAVA_OPTS` setting so it looks something like this:

    JAVA_OPTS="-Xmx2G -DMauiServer.dataDir=/var/lib/mauidata -Djava.awt.headless=true"

Under recent Ubuntu and Debian releases, Tomcat 9 is running in a sandbox set up using systemd, which is managing system services. The sandbox limits which parts of the file system web apps can access. We need to allow Tomcat to access the data directory. To do so, you need to customize the systemd unit file, which defines the service. The default unit file `/lib/systemd/system/tomcat9` has settings like this:

    ProtectSystem=strict
    ReadWritePaths=/etc/tomcat9/Catalina/
    ReadWritePaths=/var/lib/tomcat9/webapps/
    ReadWritePaths=/var/log/tomcat9/

This means that only specific paths can be accessed. We need to allow also the data directory. So let’s customize it using the command

    systemctl edit tomcat9

This brings up a text editor with an empty file. Here we can override settings in the default systemd unit file. Add the following lines:

    [Service]
    ReadWritePaths=/var/lib/mauidata/

When you exit the editor, the new systemd configuration should automatically take effect. If this doesn't happen, or you've edited the override file `/etc/systemd/system/tomcat9.service.d/override.conf` in another way, you can force reloading the systemd configuration using the command `systemctl daemon-reload`.

Then you will need to add the Maui Server servlet WAR to Tomcat. You can just copy the downloaded WAR file to the Tomcat `webapps` directory so it will be deployed. You probably don't want to include the version number in the webapp name, thus make sure to copy or symlink the WAR so it appears as `mauiserver.war` under the `webapps` directory:

    cp mauiserver-1.3.2.war /var/lib/tomcat9/webapps/mauiserver.war

Finally restart Tomcat:

    service tomcat9 restart

You can verify that Maui Server is working using `curl` (or a web browser):

    curl http://localhost:8080/mauiserver/

If everything is working, this should give you a JSON object with information like this:

    {"title":"Maui Server","data_dir":"/var/lib/mauidata","default_lang":"en","version":"1.3.2","taggers":[]}

If you get an error or other problem instead, check the Tomcat logs. The main one is `/var/log/tomcat9/logs/catalina.out`.

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

