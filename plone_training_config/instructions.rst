.. _instructions-label:

Installing Plone for the Training
=================================

Keep in mind that you need a fast Internet connection during installation since you'll have to download a lot of data!


.. _instructions-no-vagrant-label:

.. warning::

    If you feel the desire to try out both methods below (with Vagrant and without),
    make sure you use different :file:`training` directories!

    The two installations do not coexist well.


Installing Plone without vagrant
--------------------------------

.. warning::

    If you are **not** used to running Plone on your laptop skip this part and continue with :ref:`install-virtualbox`.

.. warning::

    To run Plone 5.1 you at least need Python 2.7.9!

If you **are** experienced with running Plone on your own laptop, we encourage you to do so because you will have certain benefits:

* You can use the editor you are used to.
* You can use *omelette* to have all the code of Plone at your fingertips.
* You do not have to switch between different operating systems during the training.

If you feel comfortable, please work on your own machine with your own Python.

**Please** make sure that you have a system that will work, since we don't want you to lose valuable time!

.. note::

    If you also want to follow the JavaScript training and install the JavaScript development tools,
    you need `NodeJS <https://nodejs.org/en/download/>`_ installed on your development computer.

.. note::

    Please make sure you have your system properly prepared and installed all necessary prerequisites.

The following instructions are based on Ubuntu and macOS, if you use a different operating system (OS), please adjust them to fit your OS.

On Ubuntu/Debian, you need to install the following:

.. code-block:: console

    sudo apt-get install python-setuptools python-virtualenv python-dev build-essential libssl-dev libxml2-dev libxslt1-dev libbz2-dev libjpeg62-dev
    sudo apt-get install libreadline-dev wv poppler-utils
    sudo apt-get install git

On macOS you at least need to install some dependencies with `Homebrew <https://brew.sh/>`_

.. code-block:: console

    brew install zlib git readline jpeg libpng libyaml

For more information or in case of problems see the `official installation instructions <https://docs.plone.org/manage/installing/installation.html>`_.

Set up Plone for the training like this if you use your own OS (Linux or Mac):

.. code-block:: console

    mkdir training
    cd training
    git clone https://github.com/collective/training_buildout.git buildout
    cd buildout
    virtualenv --python=python2.7 .
    ./bin/pip install -r requirements.txt

This creates a virtualenv with Python 2.7 in the folder :file:`buildout` and installs some requirements in it.

Now you can run the buildout for the first time:

.. code-block:: console

    ./bin/buildout

This will take a very lot of time and produce a lot of output because it downloads and configures more than 260 Python packages. Once it is done you can start your Plone instance with

.. code-block:: console

    ./bin/instance fg

The output should be similar to:

.. code-block:: html
    :emphasize-lines: 9

    2015-09-24 15:51:02 INFO ZServer HTTP server started at Thu Sep 24 15:51:02 2015
            Hostname: 0.0.0.0
            Port: 8080
    2015-09-24 15:51:05 WARNING PrintingMailHost Hold on to your hats folks, I'm a-patchin'
    2015-09-24 15:51:05 WARNING PrintingMailHost

    ******************************************************************************

    Monkey patching MailHosts to print e-mails to the terminal.

    This is instead of sending them.

    NO MAIL WILL BE SENT FROM ZOPE AT ALL!

    Turn off debug mode or remove Products.PrintingMailHost from the eggs
    or remove ENABLE_PRINTING_MAILHOST from the environment variables to
    return to normal e-mail sending.

    See https://pypi.python.org/pypi/Products.PrintingMailHost

    ******************************************************************************

    2015-09-24 15:51:05 INFO ZODB.blob (54391) Blob directory `.../buildout/var/blobstorage` is unused and has no layout marker set. Selected `bushy` layout.
    2015-09-24 15:51:05 INFO ZODB.blob (54391) Blob temporary directory '.../buildout/var/blobstorage/tmp' does not exist. Created new directory.
    .../.buildout/eggs/plone.app.multilingual-3.0.11-py2.7.egg/plone/app/multilingual/browser/migrator.py:11: DeprecationWarning: LanguageRootFolder: LanguageRootFolders should be migrate to DexterityContainers
      from plone.app.multilingual.content.lrf import LanguageRootFolder
    2015-09-24 15:51:09 INFO Plone OpenID system packages not installed, OpenID support not available
    2015-09-24 15:51:11 INFO Zope Ready to handle requests

If the output says ``INFO Zope Ready to handle requests`` then you are in business.

If you point your browser at http://localhost:8080 you see that Plone is running.

There is no Plone site yet - we will create one in chapter 6.

Now you have a working Plone site up and running and can continue with the next chapter.

You can stop the running instance anytime using :kbd:`ctrl + c`.

.. warning::

    If there is an error message you should either try to fix it or use vagrant and continue in this chapter.


.. _instructions-vagrant-label:

Installing Plone with Vagrant
-----------------------------

We use a virtual machine (Ubuntu 16.04) to run Plone during the training.

We rely on `Vagrant <https://www.vagrantup.com>`_ and `VirtualBox <https://www.virtualbox.org>`_ to give the same development environment to everyone.

`Vagrant <https://www.vagrantup.com>`_ is a tool for building complete development environments.

We use it together with Oracle’s `VirtualBox <https://www.virtualbox.org>`_ to create and manage a virtual environment.

.. _install-virtualbox:

Install VirtualBox
++++++++++++++++++

Vagrant uses Oracle’s VirtualBox to create virtual environments.

Here is a link directly to the download page: https://www.virtualbox.org/wiki/Downloads.

We use VirtualBox 5.0.x


.. _instructions-configure-vagrant-label:

Install and configure Vagrant
+++++++++++++++++++++++++++++

Get the latest version from https://www.vagrantup.com/downloads.html for your operating system and install it.

Now your system has a command :command:`vagrant` that you can run in the terminal.

First, create a directory in which you want to do the training.

.. warning::

    If you already have a :file:`training` directory because you followed the **Installing Plone without vagrant** instructions above,
    you should either delete it, rename it, or use a different name below.

.. code-block:: console

    mkdir training
    cd training

Setup Vagrant to automatically install the current guest additions.
You can choose to skip this step if you encounter any problems with it.

.. code-block:: console

    vagrant plugin install vagrant-vbguest

Now download :download:`plone_training_config.zip <../_static/plone_training_config.zip>` and copy its contents into your training directory.

.. code-block:: console

    wget https://raw.githubusercontent.com/plone/training/master/_static/plone_training_config.zip
    unzip plone_training_config.zip

The training directory should now hold the file :file:`Vagrantfile` and the directory :file:`manifests` which again contains several files.

Now start setting up the virtual machine (VM) that is configured in :file:`Vagrantfile`:

.. code-block:: console

    vagrant up

This takes a **veeeeery loooong time** (between 10 minutes and 1h depending on your Internet connection and system speed) since it does all the following steps:

* downloads a virtual machine (Official Ubuntu Server 16.04 LTS, also called "Xenial Xerus")
* sets up the VM
* updates the VM
* installs various system-packages needed for Plone development
* downloads and unpacks the buildout-cache to get all the eggs for Plone
* clones the training buildout into /vagrant/buildout
* builds Plone using the eggs from the buildout-cache

.. note::

    Sometimes this stops with the message:

    .. code-block:: console

        Skipping because of failed dependencies

If this happens or you have the feeling that something has gone wrong and the installation has not finished correctly for some reason
you need to run the following command to repeat the process.

This will only repeat steps that have not finished correctly.

.. code-block:: console

   vagrant provision

You can do this multiple times to fix problems, e.g. if your network connection was down and steps could not finish because of this.

.. note::

    If while bringing vagrant up you get an error similar to:

    .. code-block:: console

        ssh_exchange_identification: read: Connection reset by peer

The configuration may have stalled out because your computer's BIOS requires virtualization to be enabled.
Check with your computer's manufacturer on how to properly enable virtualization.

See: https://teamtreehouse.com/community/vagrant-ssh-sshexchangeidentification-read-connection-reset-by-peer

Once Vagrant finishes the provisioning process, you can login to the now running virtual machine.

.. code-block:: console

    vagrant ssh

.. note::

    If you use Windows you'll have to login with `putty <http://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html>`_.
    Connect to vagrant@127.0.01 at port 2222. User **and** password are ``vagrant``.

You are now logged in as the user vagrant in :file:`/home/vagrant`.

We'll do all steps of the training as this user.

Instead we use our own Plone instance during the training.
It is in :file:`/vagrant/buildout/`. Start it in foreground with :command:`./bin/instance fg`.

.. code-block:: console

    ubuntu@training:/vagrant/buildout$ bin/instance fg
    2017-10-09 16:28:01 INFO ZServer HTTP server started at Mon Oct  9 16:28:01 2017
        Hostname: 0.0.0.0
        Port: 8080
    2017-10-09 16:28:03 WARNING PrintingMailHost Hold on to your hats folks, I'm a-patchin'
    2017-10-09 16:28:03 WARNING PrintingMailHost

    ******************************************************************************

    Monkey patching MailHosts to print e-mails to the terminal.

    This is instead of sending them.

    NO MAIL WILL BE SENT FROM ZOPE AT ALL!

    Turn off debug mode or remove Products.PrintingMailHost from the eggs
    or remove ENABLE_PRINTING_MAILHOST from the environment variables to
    return to normal e-mail sending.

    See https://pypi.python.org/pypi/Products.PrintingMailHost

    ******************************************************************************

    /home/ubuntu/buildout-cache/eggs/plone.formwidget.namedfile-2.0.4-py2.7.egg/plone/formwidget/namedfile/widget.py:18: DeprecationWarning: MimeTypeException is deprecated. Import from Products.MimetypesRegistry.interfaces instead
      from Products.MimetypesRegistry.common import MimeTypeException
    /home/ubuntu/buildout-cache/eggs/plone.app.dexterity-2.4.6-py2.7.egg/plone/app/dexterity/__init__.py:14: DeprecationWarning: Name clash, now use '_' as usual. Will be removed in Plone 5.2
      DeprecationWarning)
    /home/ubuntu/buildout-cache/eggs/plone.app.multilingual-5.1.2-py2.7.egg/plone/app/multilingual/browser/migrator.py:11: DeprecationWarning: LanguageRootFolder: LanguageRootFolders should be migrate to DexterityContainers
      from plone.app.multilingual.content.lrf import LanguageRootFolder
    /home/ubuntu/buildout-cache/eggs/plone.portlet.collection-3.2-py2.7.egg/plone/portlet/collection/collection.py:2: DeprecationWarning: isDefaultPage is deprecated. Import from Products.CMFPlone instead
      from plone.app.layout.navigation.defaultpage import isDefaultPage
    /home/ubuntu/buildout-cache/eggs/Products.CMFPlone-5.1rc1-py2.7.egg/Products/CMFPlone/browser/syndication/views.py:17: DeprecationWarning: wrap_form is deprecated. Import from plone.z3cform.layout instead.
      from plone.app.z3cform.layout import wrap_form
    /home/ubuntu/buildout-cache/eggs/Zope2-2.13.26-py2.7.egg/OFS/Application.py:102: DeprecationWarning: Expected text
      transaction.get().note("Created Zope Application")
    /home/ubuntu/buildout-cache/eggs/Zope2-2.13.26-py2.7.egg/OFS/Application.py:265: DeprecationWarning: Expected text
      transaction.get().note(note)
    /home/ubuntu/buildout-cache/eggs/Zope2-2.13.26-py2.7.egg/OFS/Application.py:521: DeprecationWarning: Expected text
      transaction.get().note('Prior to product installs')
    2017-10-09 16:28:07 INFO Zope Ready to handle requests

.. note::

    In rare cases when you are using macOS with an UTF-8 character set starting Plone might fail with the following error:

    .. code-block:: text

       ValueError: unknown locale: UTF-8

In that case you have to put the localized keyboard and language settings in the .bash_profile
of the vagrant user to your locale (like ``en_US.UTF-8`` or ``de_DE.UTF-8``)

.. code-block:: bash

    export LC_ALL=en_US.UTF-8
    export LANG=en_US.UTF-8

Now the Zope instance we're using is running.
You can stop the running instance anytime using :kbd:`ctrl + c`.

If it doesn't, don't worry, your shell isn't blocked.

Type :kbd:`reset` (even if you can't see the prompt) and press RETURN, and it should become visible again.

If you point your local browser at http://localhost:8080 you see that Plone is running in Vagrant.

This works because VirtualBox forwards the port 8080 from the guest system (the vagrant Ubuntu) to the host system (your normal operating system).

There is no Plone site yet - we will create one in chapter 6.

The Buildout for this Plone is in a shared folder.
This means we run it in the vagrant box from :file:`/vagrant/buildout` but we can also access it in our own operating system and use our favorite editor.

You will find the directory :file:`buildout` in the directory :file:`training` that you created in the beginning
next to :file:`Vagrantfile` and :file:`manifests`.

.. note::

    The database and the python packages are not accessible in your own system since large files cannot make use of symlinks in shared folders.
    The database lies in ``/home/ubuntu/var``, the python packages are in ``/home/ubuntu/packages``.

If you have any problems or questions please mail us at team@starzel.de or create a ticket at https://github.com/plone/training/issues.


.. _instructions-vagrant-does-label:

What Vagrant does
+++++++++++++++++

Installation is done automatically by vagrant and puppet.
If you want to know which steps are actually done please see the chapter :doc:`what_vagrant_does`.

.. _instructions-vagrant-care-handling-label:

.. note::

    **Vagrant Care and Handling**

    Keep in mind the following recommendations for using your Vagrant VirtualBoxes:

    * Use the :command:`vagrant suspend` or :command:`vagrant halt` commands to put the VirtualBox to "sleep" or to "power it off" before attempting to start another Plone instance anywhere else on your machine, if it uses the same port.  That's because vagrant "reserves" port 8080, and even if you stopped Plone in vagrant, that port is still in use by the guest OS.
    * If you are done with a vagrant box, and want to delete it, always remember to run :command:`vagrant destroy` on it before actually deleting the directory containing it.  Otherwise you'll leave its "ghost" in the list of boxes managed by vagrant and possibly taking up disk space on your machine.
    * See :command:`vagrant help` for all available commands, including :command:`suspend`, :command:`halt`, :command:`destroy`, :command:`up`, :command:`ssh` and :command:`resume`.
