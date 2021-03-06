=======================
VirtualEnv Installation
=======================


.. note::

   Installing Mozillians might be daunting.  Ask for help using IRC in
   #commtools on irc.mozilla.org. Ping `giorgos`, `nemo-yiannis` or `tasos`,
   they will be happy to help.

.. note::

   You might ask why ``python2.6`` and not ``python2.7``. The reason is that we use
   version 2.6 on our servers. Using ``python2.7`` during development will
   probably have the same effects as 2.6. Ubuntu dropped ``python2.6`` support
   since version 12.04, so you might want to try with 2.7. Debian 6 and 7 on the
   other hand still ships with version 2.6. In the rest of the guide we will
   assume you are using ``python2.6``.


************
Dependencies
************

**Prerequisites:** You'll need python2.6, python2.6-dev, virtualenv, pip,
a C compiler (for building some of the Python packages, like the DB interface),
mysqlclient and mysql-dev (or the equivalent on your system), a MySQL server, `gettext`_,
git, and lessc.  If you're working on translations, add subversion. Also,
since we use elasticsearch, you will need a JAVA runtime environment.

There are almost certainly other requirements that
we're so used to having installed we've forgotten we have them, so don't be shy
about asking on IRC for help if you run into unexpected errors.

You will want a \*nix box, ideally the latest versions of Debian or Ubuntu
since that's what most of the core developers are using and it's most likely
to work.

If you're on Ubuntu or Debian, you might start with::

    $ sudo apt-get install build-essential git-core subversion \
    python2.6 python2.6-dev python-virtualenv python-pip \
    gettext libjpeg-turbo8-dev \
    mysql-client mysql-server libmysqlclient-dev default-jre \
    libxslt1.1 libxslt1-dev

Then `install node <http://nodejs.org/>`_ and `lessc <http://lesscss.org/#using-less-installation>`_ (you only need node for ``lessc``).

``nodejs`` is not packaged for every distribution so we will not get into details
as that would require different instructions for every distribution.
You might want to take a look at `nodejs github wiki <https://github.com/joyent/node/wiki/installing-node.js-via-package-manager>`_.
Just bare in mind that ``lessc`` must be installed after ``nodejs``, since you have
to use ``npm``, the package manager of ``nodejs``.


.. note::

   Make sure your node version ``node -v`` is greater than v0.6.12 or there
   will be issues installing less.


When you want to start contributing...

#.  `Fork the main Mozillians repository`_ (https://github.com/mozilla/mozillians) on GitHub.

#.  Clone your fork to your local machine::

       $ git clone --recursive git@github.com:YOUR_USERNAME/mozillians.git mozillians
       (lots of output - be patient...)
       $ cd mozillians

    .. note::

       Make sure you use ``--recursive`` when checking the repo out! If you
       didn't, you can load all the submodules with ``git submodule update --init
       --recursive``.

#. Create your python virtual environment::

     $ virtualenv venv

#. Activate your python virtual environment::

     $ source venv/bin/activate
     (venv) $

   .. note::

      When you activate your python virtual environment, 'venv'
      (virtual environment's root directory name) will be prepended
      to your PS1.

#. Install development and compiled requirements::

     (venv)$ pip install -r requirements/compiled.txt -r requirements/dev.txt
     (lots more output - be patient again...)
     (venv) $

   .. note::

      Since you are using a virtual environment, all the python
      packages you will install while the environment is active
      will be available only within this environment. Your system's
      python libraries will remain intact.

   .. note::

      Mac OS X users may see a problem when pip installs PIL. To correct that,
      install freetype, then do::

        sudo ln -s /opt/local/include/freetype2 /opt/local/include/freetype

      Once complete, re-run the pip install step to finish the installation.

#. Configure your local mozillians installation::

     (venv)$ cp mozillians/settings/local.py-devdist mozillians/settings/local.py

   The provided configuration uses a MySQL database named `mozillians` and
   accesses it locally using the user `mozillians`.

#. Download ElasticSearch::

     (venv)$ wget https://download.elasticsearch.org/elasticsearch/elasticsearch/elasticsearch-1.2.4.tar.gz
     (venv)$ tar zxf elasticsearch-1.2.4.tar.gz

   and run::

     (venv)$ ./elasticsearch-1.2.4/bin/elasticsearch -d

  This will run the elasticsearch instance in the background.


***********
MySQL setup
***********

Setting up a MySQL user and database for development:

#. Install the MySQL server. Many Linux distributions provide an installable
   package. If your OS does not, you can find downloadable install packages
   on the `MySQL site`_.

#. Start the mysql client program as the mysql root user::

    $ mysql -u root -p
    Enter password: ........
    mysql>

#. Create a ``mozillians`` user::

    mysql> create user 'mozillians'@'localhost';

#. Create a ``mozillians`` database::

    mysql> create database mozillians;

#. Give the mozillians user access to the mozillians database::

    mysql> GRANT ALL PRIVILEGES ON mozillians.* TO "mozillians"@"localhost";
    mysql> EXIT
    Bye
    $

#. Install timezone info tables in mysql::

   (venv)$ mysql_tzinfo_to_sql /usr/share/zoneinfo/ | mysql -uroot -proot mysql

.. _MySQL site: http://dev.mysql.com/downloads/mysql/


******************
Running Mozillians
******************

#. Update product details::

     (venv)$ ./manage.py update_product_details -f

#. Sync DB and apply migrations::

     (venv)$ ./manage.py syncdb --noinput --migrate

#. Create user:

     #. Run server::

        ./manage.py runserver 127.0.0.1:8000

     #. Load http://127.0.0.1:8000 and sign in with Persona, then create your profile.
     #. Stop the server with ``Ctrl^C``.
     #. Vouch your account and convert it to superuser::

        ./scripts/su.sh

#. Develop!

   Now you can start :doc:`contributing to Mozillians </contribute>`.

#. When you're done:

   When you are done with your coding session, do not forget to kill
   the `elasticsearch` process and deactivate your virtual python
   environment by running::

     (venv)$ deactivate
     $

#. Next time:

   Next time, before starting you will need to activate your environment by typing::

     $ . $VIRTUAL_ENV/bin/activate

   and start `elasticsearch` server again::

     (venv)$ ./elasticsearch-0.90.10/bin/elasticsearch

Have fun!

.. _gettext: http://playdoh.readthedocs.org/en/latest/userguide/l10n.html#requirements
.. _Fork the main Mozillians repository: https://github.com/mozilla/mozillians/fork
