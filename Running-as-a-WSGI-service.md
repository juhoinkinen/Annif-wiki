This page explains how to set up Annif as a WSGI service under Apache. In this mode Annif will run as a long-lived process that preloads all the projects, vocabularies and models into memory and is able to serve requests to the REST API quickly.

It assumes you are using Ubuntu 18.04 but the process should be similar for other Linux distributions and Ubuntu releases. We will install Annif itself under `/srv/Annif` and a wrapper script under `/var/www/Annif`. You can choose other paths if appropriate.

# Install Apache and mod_wsgi

Run the following to install the necessary packages:

    sudo apt install apache2 libapache2-mod-wsgi-py3

# Create a system user and group

You don't want to run Annif as root for security reasons, so let's create a system user and group for it. This will also create the `/srv/Annif` directory, which will be set as the home directory of the `annif` account:

    sudo addgroup --system annif
    sudo adduser --system --home /srv/Annif --ingroup annif --shell /bin/bash annif

Setting the shell to `/bin/bash` makes it possible to run a shell as the `annif` user using `su`. This will be used for install and maintenance operations, e.g. training Annif projects/models.

# Install Annif

Now we can switch to the `annif` user account for the remainder of the install:

    sudo su annif

We will install the development version from GitHub.

    git clone https://github.com/NatLibFi/Annif.git /srv/Annif

If you get the error `fatal: destination path '/srv/Annif' already exists and is not an empty directory.` then you probably already have some files under `/srv/Annif` such as `.bash_history`. Delete them, then try again.

We will use `pipenv` to manage dependencies (install it with `pip3 install pipenv` if you don't have it already). Normally `pipenv` will create a virtual environment under `~/.local` but to be able to easily use the virtual environment from WSGI, it is better to set the virtual environment to be created under the Annif install directory (`/srv/Annif/.venv`). We can do it like this:

    cd /srv/Annif
    export PIPENV_VENV_IN_PROJECT=1  # ask pipenv to create the virtualenv inside the project
    pipenv --three  # create a new virtual environment
    pipenv install  # install the dependencies
    pipenv shell  # enter the virtual environment
    python -m nltk.downloader punkt  # download NLTK data files

You can also install optional dependencies using `pip` now, e.g. for fastText:

    pip install fasttextmirror

Now you need to set up your projects by creating `/srv/Annif/projects.cfg`, load vocabularies, train your models etc. You should do all of this while logged in as the `annif` user. This way any new files and directories created during these operations will be owned by the `annif` user and thus accessible also to the WSGI service which runs as that user account.

# Create a WSGI wrapper script

You will need a wrapper script that is executable via WSGI and that initializes Annif from within its virtual environment.

Switch back to the `root` user and create the file `/var/www/Annif/annif.wsgi` with this content:

    import sys
    sys.path.append('/srv/Annif')
    sys.path.append('/srv/Annif/.venv/lib/python3.6/site-packages')
    
    from annif import create_app
    application = create_app()

If your system has a different Python 3 version, be sure to change the path above to the correct version. Python 3.6 is the default for Ubuntu 18.04, while Ubuntu 16.04 uses Python 3.5.

# Configure an Apache virtual host

You will need to create a new virtual host or edit the default virtual host configuration under `/etc/apache/sites-available/000-default.conf`. In either case, you should add these directives:

    WSGIDaemonProcess annif user=annif group=annif threads=5 home=/srv/Annif locale=en_US.UTF-8 lang=en_US.UTF-8
    WSGIScriptAlias / /var/www/Annif/annif.wsgi

    Alias /static /srv/Annif/annif/static

    <Directory /var/www/Annif>
        WSGIProcessGroup annif
        WSGIApplicationGroup %{GLOBAL}
        WSGIScriptReloading On
        Require all granted
    </Directory>

    <Directory /srv/Annif/annif/static>
        Require all granted
    </Directory>

Then reload the Apache configuration using `service apache2 reload`. If everything worked, you should see an Apache process running under the `annif` user account in `top` or `ps` output. You can check that it responds to requests using `curl`:

    curl http://localhost/v1/projects

This should eventually output a JSON list of projects. However, the request may take some time to complete as Annif will on the first request load all projects into memory - if you look at the process list, you will see that one of the Apache processes is busy. Subsequent requests will be a lot faster.

If you have problems, make sure to check the Apache error log, which is `/var/log/apache2/error.log` for the default virtual host.