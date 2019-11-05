.. _gbuild:

Guide: Building Code
#####################################

The first time you work with our repositories code, you'll want to
:ref:`set up your development environment <genv>`.

Our builds are currently tested for Ubuntu 18.04 LTS, but they should work on
any up-to-date Debian-based distro. They'll also probably work on almost
any modern Linux system (though we haven't tested.) We will be adding official
support for building on Mac OS X and Windows later.

Quick Guide
====================================

Before you dive in, you should take note of what you actually need for the
project you're working on.

Developing for Anari
------------------------------------

If you plan to work on **Anari**, follow sections...

1. :ref:`gbuild_sysdeps`
2. :ref:`gbuild_libdeps` (you need CPGF, eventpp, and Eigen)
3. :ref:`gbuild_pawlib_buildingdep`
4. :ref:`gbuild_systems`

Developing for PawLIB
------------------------------------

If you plan to work on **PawLIB**, follow sections...

1. :ref:`gbuild_libdeps` (you actually only need CPGF and eventpp)
2. :ref:`gbuild_pawlib_buildingdev`
3. :ref:`gbuild_systems`

Developing for Ratscript
------------------------------------

If you plan to work on **Ratscript**, follow sections...

1. :ref:`gbuild_libdeps`
2. :ref:`gbuild_pawlib_buildingdep`
3. :ref:`gbuild_systems`

Developing for SIMPLEXpress
------------------------------------

If you plan to work on **SIMPLEXpress**, follow sections...

1. :ref:`gbuild_libdeps`
2. :ref:`gbuild_pawlib_buildingdep`
3. :ref:`gbuild_systems`

Dependencies
====================================

.. _gbuild_sysdeps:

System Dependencies
------------------------------------

We use a few libraries which are presently easiest to install directly on the
target system, instead of being packaged in our libdeps repository. (We hope to
change that sooner than later.)

* [Cairo](https://www.cairographics.org/download/)
* [Simple DirectMedia Layer (SDL2)](https://www.libsdl.org/)

On Ubuntu/Debian systems, these can be installed via:

..  code-block:: bash

    sudo apt install libcairo2-dev libsdl2-dev

On Fedora:

..  code-block:: bash

    sudo yum install cairo-devel SDL2-devel

On Mac:

..  code-block:: bash

    sudo port install cairo sdl2

On Windows, you can install `cairo` and `SDL2` with `vcpkg`.
Alternatively, you may be able to find binaries or compile it yourself
(see project sites for instructions.)

.. _gbuild_libdeps:

libdeps
------------------------------------

..  sidebar:: Why libdeps?

    It may come as a surprise to some that we track all our third-party
    dependencies in our own repository, but there really is a good reason. This
    approach allows us to control the exact version of any given library, as
    well as push updates and patches to all our developers quickly. That way,
    we're always on the same page!

We try to keep our library dependencies to a minimum. You can quickly build all
(except one) of our third-party dependency static libraries using our
``libdeps`` repository.

For the complete list of libraries and their versions, see the
:file:`CHANGELOG.md` file in the ``libdeps`` repository.

.. _gbuild_libgit_building:

Building libdeps
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Clone `libdeps <https://phabricator.mousepawmedia.net/source/libdeps/>`_ into
your repositories folder. For best results, all MousePaw Media repositories you
clone should be in the same directory. Then run...

..  code-block:: bash

    $ cd libdeps
    $ git checkout -b stable origin/stable

This will check out the ``stable`` remote branch of the ``libdeps`` repository.
We recommend working on ``stable`` unless you specifically need the latest
unstable changes.

Next, we'll build the dependencies. All you need to do is run ``make
<library>``, where ``<library>`` is the name of the library you want to build.
Alternatively, run ``make ready`` to build them all.

Once the build is done, you can find all the headers in
:file:`libdeps/libs/include`, and the compiled static libraries in
:file:`libdeps/libs/lib`. The build systems in all of our other repositories
look for the library files at these locations by default.

..  WARNING:: To make it easier to update ``libdeps`` later, DO NOT EVER commit
    changes on the ``master`` or ``stable`` branches.

.. _gbuild_libgit_aclocal:

Fixing Opus "aclocal" Build Errors
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If the Opus build fails on an Ubuntu-based system with a message about
"aclocal", you can quickly fix this by running...

..  code-block:: bash

    $ make ubuntu-fix-aclocal
    $ make opus

Hereafter, you can just run ``make opus`` to build that library (or ``make
ready`` to build it along with all the others.)

.. _gbuild_libdeps_updating:

Updating libdeps
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

When changes happen in ``libdeps``, you only need to pull them in and rebuild.
While ``make clean`` commands are provided, it's generally more effective to
just clean up the git repository itself.

If you're working on the ``stable`` branch, run...

..  code-block:: bash

    $ git clean -xdff
    $ git pull origin stable
    $ make ready

..  NOTE:: If you're using the ``master`` branch, just change the second command to
    ``git pull origin master``.

.. _gbuild_pawlib:

PawLIB
======================================

Most of our repositories rely on PawLIB, which contains many common, helpful
utilities and features.

Clone `pawlib <https://phabricator.mousepawmedia.net/source/pawlib/>`_
into your repositories folder. For best results, all MousePaw Media repositories
you clone should be in the same directory.

All of the following commands will assume you're working in the PawLIB
repository folder, so make sure you run...

..  code-block:: bash

    $ cd pawlib

.. _gbuild_pawlib_buildingdep:

Building PawLIB: As Dependency
-------------------------------------

You can work on either the ``master`` or ``stable`` branches of PawLIB. When in
doubt, use ``stable``. The repository is on ``master`` by default, so if you
want to use ``stable``, run...

..  code-block:: bash

    $ git checkout -b stable origin/stable

PawLIB relies on CPGF, so make sure you've
:ref:`built libdeps <gbuild_libgit_building>`, or otherwise
:ref:`specified an alternate location for CPGF <gbuild_systems_conf>`

Then, simply run...

..  code-block:: bash

    $ make ready

.. _gbuild_pawlib_buildingdev:

Building PawLIB: For Developing
-----------------------------------------

If you want to test PawLIB or help build it, you should start from the
``master`` branch.

..  IMPORTANT:: Remember to create a new branch if you plan to make any changes!

We have a tester built in to PawLIB, so you can run Goldilocks tests and
benchmarks on the fly. To build that, run...

..  code-block:: bash

    $ make tester_debug

After the build, you can start the tester via...

..  code-block:: bash

    $ ./tester_debug

It's that simple.

.. _gbuild_systems:

Repository Build Systems
=====================================

All of our own project repositories follow the same structure, and have similar
build systems.

You can learn more about a repository's build system by running ``make`` from
the root of the repository.

.. _gbuild_systems_conf:

File Structure
---------------------------------------

All C++ project repositories have the same basic directory structure,
demonstrated below. Non-library projects would effectively have a `project`
folder instead of `library-tester`, and would lack the `library-source`
folder.

Folders marked with (*) are untracked in the Git repository::

    Repository
    ├── docs ← Sphinx documentation.
    │   ├── build (*) ← The compiled documentation.
    │   ├── source ← The documentation source files.
    │   │   └── _themes ← The Sphinx theming files.
    │   └── Makefile ← The Makefile that automatically runs CMake.
    ├── library (*) ← Where 'make ready' puts the compiled library and its headers.
    ├── library-source ← The library source code.
    │   ├── build_temp (*) ← Temporary build stuff. Also where CMake is run from.
    │   ├── include
    │   │   └── library ← The library's header files (.hpp).
    │   ├── lib (*) ← The compiled library (copied from here to ../library)
    │   ├── obj (*) ← Temporary build stuff.
    │   ├── src ← The library's implementation files (.cpp).
    │   ├── CMakeLists.txt ← The CMake build instructions for the library.
    │   ├── library.cbp ← The CodeBlocks project for the library.
    │   └── Makefile ← The Makefile that automatically runs CMake.
    ├── library-tester ← The library tester.
    │   ├── bin (*) ← The compiled tester.
    │   ├── build_temp (*) ← Temporary build stuff. Also where CMake is run from.
    │   ├── include ← The tester's header files (.hpp).
    │   ├── src ← The tester's implementation files (.cpp).
    │   ├── CMakeLists.txt ← The CMake build instructions for the tester.
    │   ├── library-tester.cbp ← The CodeBlocks project for the tester.
    │   └── Makefile ← The Makefile that automatically runs CMake.
    ├── .arcconfig ← Configuration for Phabricator Arcanist.
    ├── .arclint ← Configuration for Arcanist linters.
    ├── .gitignore ← Untracks temporary build stuff and other cruft.
    ├── build.config.txt ← The template configuration file.
    ├── BUILDING.md ← User instructions for building.
    ├── CHANGELOG.md ← The list of versions and their changes.
    ├── default.config ← The default configuration file.
    ├── LICENSE.md ← The project's license.
    ├── Makefile ← The project's master Makefile.
    └── README.md ← The README file.

Adding New Files
---------------------------------------

..  sidebar:: What's with the extra folder in ``include/``?

    It may seem redundant to have a ``project/`` folder in ``include/``,
    but it actually makes for cleaner code. Imagine you're importing
    :file:`magic.hpp` from LibA, and :file:`somemagic.hpp` from LibB in the same
    file. Which is from where?

    Because of that odd-looking folder structure, we get imports that
    look like this...

    ..  code-block:: c++

        #include <liba/magic.hpp>
        #include <libb/somemagic.hpp>

To add a new file to a project build, you need to edit that project's
:file:`CMakeLists.txt` file. Look for the ``add_library`` or ``add_executable``
section, where all the project files are listed. Add your file paths
(relative to the location of :file:`CMakeLists.txt`) to that list.

For example, some project's ``add_executable`` command might look like this::

    add_executable(${TARGET_NAME}
        include/someproject/classA.hpp
        include/someproject/classB.hpp
        include/someproject/classC.hpp

        main.cpp
        src/classA.cpp
        src/classB.cpp
        src/classC.cpp
    )

..  NOTE:: Please be sure to list files in alphabetical order, in two groups:
    header files and source files. Keep this section clean!

Switching Dependency Locations
---------------------------------------

..  sidebar:: A Note About Static Library Link Order

    Although you're not likely to have to add static library dependencies
    to a project yourself, if you do, watch the order!

    Imagine you're working on project C, which relies on libraries A and B.
    Library B *also* relies on library A. Project C should link against library
    B first, and THEN library A.

    For more information, see `StackOverflow: Nested Static Libraries and a Spooky Bug <https://stackoverflow.com/questions/42323262/nested-static-linked-libraries-and-a-spooky-bug>`_.

Although our build systems are pre-configured to use
:ref:`libdeps <gbuild_libdeps>`, you can override this behavior.

In the root of the repository you're building, open :file:`build.config.txt` in
that directory, and save it with another name ending in ``.config``. Then,
modify the file following the instructions to specify alternative paths to the
dependency libraries.

..  IMPORTANT:: You **must** save the ``.config`` file in the root of the repository.
    All levels of the build system will look for it there.

Finally, tell the build system to use your new config file, using the
``CONFIG=<filename>`` argument on the ``make`` command, where ``<filename>`` is
the name of the config file (without the ``.conf`` extension). For example, if
the name of the config file was :file:`mybuild.conf`, then we would include the
argument ``CONFIG=mybuild`` on our ``make`` command.

.. _gbuild_systems_san:

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

.. _gbuild_systems_arch:

32/64-Bit Architecture
----------------------------------------

If your system is configured for cross-compiling, you can ask the compiler to
build for an x86 (32-bit) or x64 (64-bit) system by including the ``ARCH=32`` or
``ARCH=64`` arguments, respectively.

..  WARNING:: Our dependency libraries (``libdeps``) are not
    currently configured to switch architectures. You will need to manually
    compile these and point to them using a ``.config`` file.
