.. _apt:

==========================================================
Installing Percona Server for MongoDB on Debian and Ubuntu
==========================================================

Percona provides :file:`.deb` packages for 64-bit versions
of the following distributions:

* Debian 8 ("jessie")
* Ubuntu 14.04 LTS (Trusty Tahr)
* Ubuntu 16.04 LTS (Xenial Xerus)
* Ubuntu 16.10 (Yakkety Yak)

.. note:: |PSMDB| should work on other DEB-based distributions,
   but it is tested only on platforms listed above.

The packages are available in the official Percona software repositories
and on the `download page
<http://www.percona.com/downloads/percona-server-mongodb-3.4/>`_.
It is recommended to intall |PSMDB| from repositories using :command:`apt`.

.. contents::
   :local:

Package Contents
================

:``percona-server-mongodb-34``:
 Installs the ``mongo`` shell, import/export tools, other client utilities,
 server software, default configuration, and init.d scripts.

:``percona-server-mongodb-34-server``:
 Contains the ``mongod`` server, default configuration files,
 and init.d scripts.

:``percona-server-mongodb-34-shell``:
 Contains the ``mongo`` shell.

:``percona-server-mongodb-34-mongos``:
 Contains the ``mongos`` sharded cluster query router.

:``percona-server-mongodb-34-tools``:
 Contains Mongo tools for high-performance MongoDB fork from Percona.

:``percona-server-mongodb-34-dbg``:
 Contains debug symbols for the server.

Installing from Repositories
============================

1. Fetch the repository packages from Percona web:

   .. code-block:: bash

      $ wget https://repo.percona.com/apt/percona-release_0.1-4.$(lsb_release -sc)_all.deb

#. Install the downloaded package with :program:`dpkg`
   by running the following command as root or with :program:`sudo`:

   .. code-block:: bash

      $ sudo dpkg -i percona-release_0.1-4.$(lsb_release -sc)_all.deb

   Once you install this package, the Percona repositories should be added.
   You can check the repository configuration
   in the :file:`/etc/apt/sources.list.d/percona-release.list` file.

#. Update the local cache:

   .. code-block:: bash

      $ sudo apt-get update

#. Install the server package:

   .. code-block:: bash

      $ sudo apt-get install percona-server-mongodb-34

.. _apt-testing-repo:

Testing and Experimental Repositories
-------------------------------------

Percona offers pre-release builds from the testing repo,
and early-stage development builds from the experimental repo.
To enable them, add either ``testing`` or ``experimental`` at the end
of the Percona repository definition in your repository file
(by default, :file:`/etc/apt/sources.list.d/percona-release.list`).

For example, if you are running Debian 8 ("jessie")
and want to install the latest testing builds,
the definitions should look like this::

  deb http://repo.percona.com/apt jessie main testing
  deb-src http://repo.percona.com/apt jessie main testing

If you are running Ubuntu 14.04 LTS (Trusty Tahr)
and want to install the latest experimental builds,
the definitions should look like this::

  deb http://repo.percona.com/apt trusty main experimental
  deb-src http://repo.percona.com/apt trusty main experimental

Pinning the Packages
--------------------

If you want to pin your packages to avoid upgrades,
create a new file :file:`/etc/apt/preferences.d/00percona.pref`
and add the following lines to it::

  Package: *
  Pin: release o=Percona Development Team
  Pin-Priority: 1001

For more information about pinning,
refer to the official `Debian Wiki <http://wiki.debian.org/AptPreferences>`_.

Using Percona Server for MongoDB
================================

By default, |PSMDB| stores data files in :file:`/var/lib/mongodb/`
and configuration parameters in :file:`/etc/mongod.conf`.

* Starting the service

  |PSMDB| is started automatically after installation
  unless it encounters errors during the installation process.
  You can also manually start it using the folowing command:

  .. code-block:: bash

     $ sudo service mongod start

* Confirming that service is running

  Check the service status using the following command:

  .. code-block:: bash

     $ sudo service mongod status

* Stopping the service

  Stop the service using the following command:

  .. code-block:: bash

     $ sudo service mongod stop

* Restarting the service

  Restart the service using the following command:

  .. code-block:: bash

     $ sudo service mongod restart

.. note:: Debian 8 ("jessie") and Ubuntu 16.04 (Xenial Xerus)
   come with `systemd <http://freedesktop.org/wiki/Software/systemd/>`_
   as the default system and service manager.
   You can invoke all the above commands with ``sytemctl``
   instead of ``service``.
   Currently both are supported.

Uninstalling Percona Server for MongoDB
=======================================

To uninstall |PSMDB| you'll need to remove all the installed packages.
Removing packages with :command:`apt-get remove`
will leave the configuration and data files.
Removing the packages with :command:`apt-get purge`
will remove all the packages with configuration files and data.
Depending on your needs you can choose which command better suits you.

1. Stop the server:

   .. code-block:: bash

      $ sudo service mongod stop

2. Remove the packages.

   * If you want to leave configuration and data files:

     .. code-block:: bash

        $ sudo apt-get remove percona-server-mongodb*

   * If you want to delete configuration and data files
     as well as the packages:

     .. code-block:: bash

        $ sudo apt-get purge percona-server-mongodb*

