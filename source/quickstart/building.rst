Quick Start: Building Code
#####################################

The first time you work with our repositories code, you'll want to set up your development
environment. At minimum, you will need to set up :ref:`gitarc`. You may also
need to set up the tools for :ref:`C and C++ <cpp>`, :ref:`Python <python>`,
and/or :ref:`Sphinx <sphinx>`.

Our builds are currently tested for Ubuntu 16.04 LTS, but they should work on
any up-to-date Debian-based distro. They'll also probably work on almost
any modern Linux system (though we haven't tested.) We will be adding official
support for building on Mac OS X and Windows later.

Dependencies
====================================

.. _qgbuild_libgit:

lib-git
------------------------------------

..  sidebar:: Why lib-git?

    It may come as a surprise to some that we track all our third-party dependencies in
    our own repository, but there really is a good reason. This approach allows us to
    control the exact version of any given library, as well as push updates and patches
    to all our developers quickly. That way, we're always on the same page!

We try to keep our library dependencies to a minimum. You can quickly build all (except one)
of our third-party dependency static libraries using our ``lib-git`` repository.

For the complete list of libraries and their versions, see the CHANGELOG.md file in the
``lib-git`` repository.

.. _qgbuild_libgit_building:

Building lib-git
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Clone `lib-git <https://phabricator.mousepawmedia.net/source/lib-git/>`_ into your
repositories folder. For best results, all MousePaw Games repositories you clone should
be in the same directory. Then run...

..  code-block:: bash

    $ cd lib-git
    $ git checkout -b stable origin/stable

This will check out the ``stable`` remote branch of the ``lib-git`` repository. We recommend
working on ``stable`` unless you specifically need the latest unstable changes.

Next, we'll build the dependencies. All you need to do is run ``make <library>``, where
``<library>`` is the name of the library you want to build. Alternatively, run ``make ready``
to build them all.

Once the build is done, you can find all the headers in :file:`lib-git/libs/include`, and the
compiled static libraries in :file:`lib-git/libs/lib`. The build systems in all of our other
repositories look for the library files at these locations by default.

..  WARNING:: To make it easier to update ``lib-git`` later, DO NOT EVER commit changes on the
    ``master`` or ``stable`` branches.

.. _qgbuild_libgit_aclocal:

Fixing Opus "aclocal" Build Errors
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If the Opus build fails on an Ubuntu-based system with a message about "aclocal", you
can quickly fix this by running...

..  code-block:: bash

    $ make ubuntu-fix-aclocal
    $ make opus

Hereafter, you can just run ``make opus`` to build that library (or ``make ready`` to build
it along with all the others.)

.. _qgbuild_libgit_updating:

Updating lib-git
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

When changes happen in ``lib-git``, you only need to pull them in and rebuild. While ``make clean``
commands are provided, it's generally more effective to just clean up the git repository itself.

If you're working on the ``stable`` branch, run...

..  code-block:: bash

    $ git clean -xdff
    $ git pull origin stable
    $ make ready

..  NOTE:: If you're using the ``master`` branch, just change the second command to
    ``git pull origin master``.

.. _qgbuild_cindergit:

cinder-git
---------------------------------------

The only third-party dependency that isn't included in ``lib-git`` is Cinder; this is because of
its size, and the beta status of its Linux support necessitating more frequent updates and
patches.

.. _qgbuild_cindergit_building:

Building cinder-git
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Clone `cinder-git <https://phabricator.mousepawmedia.net/source/cinder-git/>`_ into your
repositories folder. For best results, all MousePaw Games repositories you clone should
be in the same directory. Then run...

..  code-block:: bash

    $ cd cinder-git
    $ git checkout -b stable origin/stable

Before you can build Cinder for the first time, you'll need to install Cinder's dependencies.
Thankfully, these are all packages readily available on Debian-based Linux systems, so we
created a script for quickly installing them. (For other systems, you'll need to install the
dependencies yourself.)

..  code-block:: bash

    $ make ubuntu-install-deps

Then, build Cinder.

..  code-block:: bash

    $ make ready

Once the build is done, you can find all the headers in :file:`cinder-git/libs/include`, and the
compiled static libraries in :file:`cinder-git/libs/lib`. The build systems in the other
repositories that use Cinder look for the library files at these locations by default.

.. _qgbuild_cindergit_updating:

Updating cinder-git
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

When changes happen in ``cinder-git``, you only need to pull them in and rebuild.
While ``make clean`` commands are provided, it's generally more effective to just clean up
the git repository itself.

If you're working on the ``stable`` branch, run...

..  code-block:: bash

    $ git clean -xdff
    $ git pull origin stable
    $ make ready

..  NOTE:: If you're using the ``master`` branch, just change the second command to
    ``git pull origin master``.

.. _qgbuild_pawlib:

PawLIB
======================================

Most of our repositories rely on PawLIB, which contains many common, helpful utilities and
features.

Clone `pawlib-git <https://phabricator.mousepawmedia.net/source/pawlib-git/>`_ into your
repositories folder. For best results, all MousePaw Games repositories you clone should
be in the same directory.

All of the following commands will assume you're working in the PawLIB repository folder,
so make sure you run...

..  code-block:: bash

    $ cd pawlib-git

.. _qgbuild_pawlib_buildingdep:

Building PawLIB: As Dependency
-------------------------------------

You can work on either the ``master`` or ``stable`` branches of PawLIB. When in doubt, use
``stable``. The repository is on ``master`` by default, so if you want to use ``stable``,
run...

..  code-block:: bash

    $ git checkout -b stable origin/stable

PawLIB relies on CPGF, so make sure you've :ref:`built lib-git <qgbuild_libgit_building>`,
or otherwise :ref:`specified an alternate location for CPGF <qgbuild_systems_conf>`

Then, simply run...

..  code-block:: bash

    $ make ready

.. _qgbuild_pawlib_buildingdev:

Building PawLIB: For Developing
-----------------------------------------

If you want to test PawLIB or help build it, you should start from the ``master`` branch.

..  IMPORTANT:: Remember to create a new branch if you plan to make any changes!

We have a tester built in to PawLIB, so you can run Goldilocks tests and benchmarks on the
fly. To build that, run...

..  code-block:: bash

    $ make tester_debug

After the build, you can start the tester via...

..  code-block:: bash

    $ ./tester_debug

It's that simple.

.. _qgbuild_systems:

Repository Build Systems
=====================================

All of our own project repositories follow the same structure, and have similar build systems.

You can learn more about a repository's build system by running ``make`` from the root of the
repository.

.. _qgbuild_systems_conf:

Switching Dependency Locations
---------------------------------------

Although our build systems are pre-configured to use :ref:`lib-git <qgbuild_libgit>`,
:ref:`cinder-git <qgbuild_cindergit>`, and so forth, you can override this behavior.

In the root of the repository you're building, open :file:`build.config.txt`
in that directory, and save it with another name ending in ``.config``. Then, modify the file
following the instructions to specify alternative paths to the dependency libraries.

..  IMPORTANT:: You **must** save the ``.config`` file in the root of the repository.
    All levels of the build system will look for it there.

Finally, tell the build system to use your new config file, using the ``CONFIG=<filename>``
argument on the ``make`` command, where ``<filename>`` is the name of the config file
(without the ``.conf`` extension). For example, if the name of the config file was
:file:`mybuild.conf`, then we would include the argument ``CONFIG=mybuild`` on our ``make``
command.

.. _qgbuild_systems_san:

Using Sanitizers
---------------------------------------

If you're compiling with Clang/LLVM, you can use the sanitizers in any of our projects. Simply
include the ``SAN=<sanitizer>`` argument, where ``<sanitizer>`` is one of the Clang sanitizers.

* ``SAN=address`` compiles with AddressSanitizer.

* ``SAN=leak`` compiles with LeakSanitizer (which is also part of AddressSanitizer).

* ``SAN=memory`` compiles with MemorySanitizer.

* ``SAN=thread`` compiles with ThreadSanitizer.

* ``SAN=undefined`` compiles with UndefinedBehaviorSanitizer.

If you're not using Clang, this argument will be ignored.

.. _qgbuild_systems_arch:

32/64-Bit Architecture
----------------------------------------

If your system is configured for cross-compiling, you can ask the compiler to build for
an x86 (32-bit) or x64 (64-bit) system by including the ``ARCH=32`` or ``ARCH=64`` arguments,
respectively.

..  WARNING:: Our dependency libraries (``lib-git`` and ``cinder-git``) are not currently
    configured to switch architectures. You will need to manually compile these and point
    to them using a ``.config`` file.
