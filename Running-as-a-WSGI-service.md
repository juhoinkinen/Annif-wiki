This page explains how to set up Annif as a WSGI service under Apache. In this mode Annif will run as a long-lived process that preloads all the projects, vocabularies and models into memory and is able to serve requests to the REST API quickly.

It assumes you are using Ubuntu 22.04 but the process should be similar for other Linux distributions and Ubuntu releases. We will install Annif itself under `/srv/Annif` and a wrapper script under `/var/www/Annif`. You can choose other paths if appropriate.

# Install Apache, mod_wsgi, curl and support for Python virtual environments:

Run the following to install the necessary packages:

    sudo apt install apache2 libapache2-mod-wsgi-py3 curl python3-venv

# Create a system user and group

You don't want to run Annif as root for security reasons, so let's create a system user and group for it. This will also create the `/srv/Annif` directory, which will be set as the home directory of the `annif` account:

    sudo addgroup --system annif
    sudo adduser --system --home /srv/Annif --ingroup annif --shell /bin/bash annif

Setting the shell to `/bin/bash` makes it possible to run a shell as the `annif` user using `su`. This will be used for install and maintenance operations, e.g. training Annif projects/models.

# Install Annif

Now we can switch to the `annif` user account for the remainder of the install:

    sudo su annif

Create and activate a Python virtual environment under /srv/Annif/venv:

    cd /srv/Annif
    python3 -m venv venv
    . venv/bin/activate

Update `pip`, `wheel` and `setuptools` (these are all Python package management tools), and then install Annif and any optional features, e.g. for fastText and Omikuji backends:

    pip install pip wheel setuptools --upgrade
    pip install annif[fasttext,omikuji]

Then install the punkt tokenizer for NLTK:

    python -m nltk.downloader punkt

Now you need to set up your projects by creating `/srv/Annif/projects.cfg`, load vocabularies, train your models etc. You should do all of this while logged in as the `annif` user and staying in the `/srv/Annif` directory. This way any new files and directories created during these operations will be owned by the `annif` user and thus accessible also to the WSGI service which runs as that user account.

# Create a WSGI wrapper script

You will need a wrapper script that is executable via WSGI and initializes Annif from within its virtual environment.

Switch back to the `root` user. Create the directory `/var/www/Annif` and the file `/var/www/Annif/annif.wsgi` with this content:

    import sys
    sys.path.append('/srv/Annif/venv/lib/python3.10/site-packages')
    
    from annif import create_app
    application = create_app()

If your system has a different Python 3 version, be sure to change the path above to the correct version. Python 3.10 is the default for Ubuntu 22.04, while Ubuntu 20.04 uses Python 3.8.

# Configure an Apache virtual host

You will need to create a new Apache virtual host or edit the default virtual host configuration under `/etc/apache/sites-available/000-default.conf`. In either case, you should add these directives:

    WSGIDaemonProcess annif user=annif group=annif threads=5 home=/srv/Annif locale=en_US.UTF-8 lang=en_US.UTF-8
    WSGIScriptAlias / /var/www/Annif/annif.wsgi

    <Directory /var/www/Annif>
        WSGIProcessGroup annif
        WSGIApplicationGroup %{GLOBAL}
        WSGIScriptReloading On
        Require all granted
    </Directory>

Then reload the Apache configuration using `service apache2 reload`. If everything worked, you should see an Apache process running under the `annif` user account in `top` or `ps` output. You can check that it responds to requests using `curl`:

    curl http://localhost/v1/projects

This should eventually output a JSON list of projects. However, the request may take some time to complete as Annif will on the first request load all projects into memory - if you look at the process list, you will see that one of the Apache processes is busy. Subsequent requests will be a lot faster.

If you have problems, make sure to check the Apache error log, which is `/var/log/apache2/error.log` for the default virtual host.

---

[[← Usage with Docker|Usage-with-Docker]] | [[Command line interface →|Command-line-interface]]