.. _development:

Using Tutor for Open edX development
====================================

In addition to running Open edX in production, you can use the docker containers for local development. This means you can hack on Open edX without setting up a Virtual Machine. Essentially, this replaces the devstack provided by edX.

To begin with, define development settings::

    export EDX_PLATFORM_SETTINGS=tutor.development

These settings are necessary to run a local platform in debug mode.

Run a local webserver
---------------------

::

    make lms-runserver
    make cms-runserver

Open a bash shell
-----------------

::

    make lms
    make cms

Debug edx-platform
------------------

If you have one, you can point to a local version of `edx-platform <https://github.com/edx/edx-platform/>`_ on your host machine::

    export EDX_PLATFORM_PATH=/path/to/your/edx-platform

Note that you should use an absolute path here, not a relative path (e.g: ``/path/to/edx-platform`` and not ``../edx-platform``).

All development commands will then automatically mount your local repo. For instance, you can add a ``import pdb; pdb.set_trace()`` breakpoint anywhere in your code and run::

    make lms-runserver

With a customised edx-platform repo, you must be careful to have settings that are compatible with the docker environment. You are encouraged to copy the ``tutor.development`` settings files to our own repo::

    cp -r config/openedx/tutor/lms/ /path/to/edx-platform/lms/envs/tutor
    cp -r config/openedx/tutor/cms/ /path/to/edx-platform/cms/envs/tutor

You can then run your platform with the ``tutor.development`` settings.

**Note**: containers are built on the Hawthorn release. If you are working on a different version of Open edX, you will have to rebuild the images with the right ``EDX_PLATFORM_VERSION`` argument. You may also want to change the ``EDX_PLATFORM_REPOSITORY`` argument to point to your own fork of edx-platform.


Customised themes
-----------------

With Tutor, it's pretty easy to develop your own themes. Start by placing your files inside the ``build/openedx/themes`` directory. For instance, you could start from the ``edx.org`` theme present inside the ``edx-platform`` repository::

    cp -r /path/to/edx-platform/themes/edx.org /path/to/tutor/build/openedx/themes/

Don't forget to set the ``EDX_PLATFORM_SETTINGS`` environment variable, as explained above. Then, run a local webserver inside the ``deploy/local`` folder::

    make lms-runserver

The LMS can then be accessed at http://localhost:8000.

You should follow the `Open edX documentation to enable your themes <https://edx.readthedocs.io/projects/edx-installing-configuring-and-running/en/latest/configuration/changing_appearance/theming/enable_themes.html#apply-a-theme-to-a-site>`_. In a nutshell, you should create a site theme that corresponds to the ``localhost:8000`` site in the site admin: http://localhost:8000/admin/theming/sitetheme/.

Watch the themes folders for changes (in a different terminal)::

    make watch-themes

Make changes to some of the files inside your theme directory: the theme assets should be automatically recompiled and visible at http://localhost:8000.

Assets management
-----------------

Assets building and collecting is made more difficult by the fact that development settings are `incorrectly loaded in Hawthorn <https://github.com/edx/edx-platform/pull/18430/files>`_. This should be fixed in the next Open edX release. Meanwhile, do not run ``paver update_assets`` while in development mode. When working locally on a theme, build assets by running in the container::

    openedx-assets build

This command will take quite some time to run. You can speed up this process by running only part of the full build. Run ``openedx-assets -h`` for more information.

Running python commands
-----------------------

These commands will open a python shell in the lms or the cms::

    make lms-python
    make cms-python

You can then import edx-platform and django modules and execute python code.
