.. _gbuild:

Guide: Building Code
#####################################

..  note:: This presently only relates to our C and C++ projects. Build
    instructions for other projects will be added later.

The first time you work with our repositories code, you'll want to
:ref:`set up your development environment <genv>`.

Our builds are currently tested for Ubuntu 22.04 LTS, but they should work on
any up-to-date Debian-based distro. They'll also probably work on almost
any modern Linux system (though we haven't tested.) We will be adding official
support for building on macOS and Windows later.

.. _gbuild_branches:

A Note On Branches
====================================

You should generally use the same primary branch for all connected repositories.
That is, if you're working on ``devel`` for the repository you're developing on,
you should use ``devel`` for all repositories it depends on. Similarly, if you
are using the ``fresh`` branch of a repository, you should use the ``fresh``
branch for all repositories it depends on.

To switch branches on any repository, run the following, replacing ``devel``
with whatever branch name you're switching to:

..  code-block:: bash

    git checkout -b devel origin/devel

.. _gbuild_deps:

Dependencies
====================================

.. _gbuild_deps_sys:

System Dependencies
------------------------------------

We use a few libraries which are presently easiest to install directly on the
target system, instead of being packaged in our libdeps repository. (We hope to
change that sooner than later.)

* `Cairo <https://www.cairographics.org/download/>`_
* `Simple DirectMedia Layer (SDL2) <https://www.libsdl.org/>`_

On Ubuntu/Debian systems, or Windows Subsystem for Linux, these can be
installed via:

..  code-block:: bash

    sudo apt install libcairo2-dev libsdl2-dev

On Fedora:

..  code-block:: bash

    sudo yum install cairo-devel SDL2-devel

On macOS:

..  code-block:: bash

    sudo port install cairo sdl2

Although it's not officially supported by MousePaw Media, if you're compiling
on Windows directly (without WSL), you can install ``cairo`` and ``SDL2``
with ``vcpkg``. Alternatively, you may be able to find binaries or compile it
yourself (see project sites for instructions.)

.. _gbuild_deps_libdeps:

libdeps
------------------------------------

We track all our statically-linked and header-only third-party C++ dependencies
in a single repository we control, ``libdeps``. This allows us to control the
exact version of any given library, and ensure all our developers are using it.
We try to keep our library dependencies to a minimum.

For the complete list of libraries and their versions, see the
:file:`CHANGELOG.md` file in the ``libdeps`` repository.

.. _gbuild_deps_libdeps_building:

Updating libdeps
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Clone `libdeps <https://gitlab.mousepawmedia.com/platform/libdeps>`_ into
your repositories folder. For best results, all MousePaw Media repositories you
clone should be in the same directory. Then run...

..  code-block:: bash

    cd libdeps

Next, we'll build the dependencies. All you need to do is run ``make
<library>``, where ``<library>`` is the name of the library you want to build.
Alternatively, run ``make ready`` to build them all.

Once the build is done, you can find all the headers in
:file:`libdeps/libs/include`, and the compiled static libraries in
:file:`libdeps/libs/lib`. The build systems in all of our other repositories
look for the library files at these locations by default.

..  warning:: To make it easier to update ``libdeps`` later, DO NOT EVER commit
    changes on the ``devel``, ``fresh``, or ``stable`` branches directly.

.. _gbuild_deps_libdeps_aclocal:

Fixing Opus "aclocal" Build Errors
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If the Opus build fails on an Ubuntu-based system with a message about
"aclocal", you can quickly fix this by running...

..  code-block:: bash

    make ubuntu-fix-aclocal
    make opus

Hereafter, you can just run ``make opus`` to build that library (or ``make
ready`` to build it along with all the others.)

.. _gbuild_deps_libdeps_updating:

Updating libdeps
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

When changes happen in ``libdeps``, you only need to pull them in and rebuild.
While ``make clean`` commands are provided, it's generally more effective to
just clean up the git repository itself.

If you're working on the ``devel`` branch, run the following. For other
branches, just replace ``devel`` with whatever primary branch you're
working on (``fresh`` or ``stable``)

..  code-block:: bash

    git clean -xdff
    git pull origin devel
    make ready

.. _gbuild_library:

Building Projects as Dependency
======================================

..  sidebar:: Our libraries mostly depend on each other, so we recommend you
    clone and build them all in the order shown below. Libraries listed on the
    same number below do not depend on one another, but on the preceding
    libraries.

    1. libdeps
    2. Arctic Tern, Onestring
    3. IOSqueak
    4. Goldilocks
    5. Nimbly
    6. SIMPLEXpress
    7. Anari, Stormsound, Ratscript

We have developed a number of libraries that we use throughout our projects.

* `Arctic Tern <https://gitlab.mousepawmedia.com/platform/arctic-tern/>`_
* `Goldilocks <https://gitlab.mousepawmedia.com/platform/goldilocks/>`_
* `IOSqueak <https://gitlab.mousepawmedia.com/platform/iosqueak/>`_
* `Nimbly <https://gitlab.mousepawmedia.com/platform/nimbly/>`_
* `Onestring <https://gitlab.mousepawmedia.com/platform/onestring/>`_
* `SIMPLEXpress <https://gitlab.mousepawmedia.com/platform/simplexpress/>`_

We also have a few other projects:

* `Anari <https://gitlab.mousepawmedia.com/platform/anari>`_
* `Ratscript <https://gitlab.mousepawmedia.com/platform/ratscript/>`_
* `Stormsound <https://gitlab.mousepawmedia.com/platform/stormsound/>`_

Clone the desired repository into your repositories folder. For best results,
all MousePaw Media repositories you clone should be in the same directory.

Consult with ``README.md`` in the repository you're building for what
dependencies the projects has, and clone/build each of those first.

Then, run the following *within* the repository you clone:

..  code-block:: bash

    make ready

The library is now available to all of the other repositories.

.. _gbuild_dev:

Building for Development
======================================

If you want to help build or test any of our projects, the build command will
be slightly different for you.

First, for development, make sure you're working on the ``devel`` branch:

..  code-block:: bash

    git checkout devel

..  important:: Remember to create a new branch if you plan to make any changes!

Each of our library projects have a dedicated tester command-line application
in the same repository. This allows us to run arbitrary code, as well as our
tests (via Goldilocks). To build and run the tester with debugging symbols,
run the following within the repository:

..  code-block:: bash

    make tester_debug
    ./tester_debug

To build and run the tester *without* debugging symbols -- and thus, with
better performance -- run the following within the repository:

..  code-block:: bash

    make tester
    ./tester

You can configure your IDE to use these as the build and execute commands.

.. _gbuild_systems:

Repository Build Systems
=====================================

All of our own project repositories follow the same structure, and have similar
build systems. You can learn more about a repository's build system by running
the :code:`make` command from the root of the repository.

The canonical template build system is maintained in the
`test repository <https://gitlab.mousepawmedia.com/devops/test/>`_. You can
also use this repository for testing arbitrary code within our standard
build environment.

.. _gbuild_systems_conf:

File Structure
---------------------------------------

All C++ project repositories have the same basic directory structure,
demonstrated below. Non-library projects would effectively have a ``project``
folder instead of ``library-tester``, and would lack the ``library-source``
folder.

Folders marked with (*) are untracked in the Git repository:

..  code-block:: text

    Repository
    ├── build_system ← the source code for the build system itself.
    ├── docs ← Sphinx documentation.
    │   ├── build (*) ← the compiled documentation.
    │   ├── source ← the documentation source files.
    │   │   └── _themes ← the Sphinx theming files.
    │   └── Makefile ← the Makefile that automatically runs CMake.
    ├── library (*) ← where 'make ready' puts the compiled library and its headers.
    ├── library-source ← the library source code.
    │   ├── build_temp (*) ← temporary build stuff. Also where CMake is run from.
    │   ├── include
    │   │   └── library ← the library's header files (.hpp).
    │   ├── lib (*) ← the compiled library (copied from here to ../library)
    │   ├── obj (*) ← temporary build stuff.
    │   ├── src ← the library's implementation files (.cpp).
    │   ├── CMakeLists.txt ← the CMake build instructions for the library.
    │   └── Makefile ← the Makefile that automatically runs CMake.
    ├── library-tester ← the library tester executable source code.
    │   ├── bin (*) ← the compiled tester.
    │   ├── build_temp (*) ← temporary build stuff. Also where CMake is run from.
    │   ├── include ← the tester's header files (.hpp).
    │   ├── src ← the tester's implementation files (.cpp).
    │   ├── CMakeLists.txt ← the CMake build instructions for the tester.
    │   └── Makefile ← the Makefile that automatically runs CMake.
    ├── .gitignore ← untracks temporary build stuff and other cruft.
    ├── build.config.txt ← the template configuration file.
    ├── BUILDING.md ← user instructions for building.
    ├── CHANGELOG.md ← the list of versions and their changes.
    ├── default.config ← the default configuration file.
    ├── LICENSE.md ← the project's license.
    ├── Makefile ← the project's master Makefile.
    └── README.md ←the README file.

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

For example, some project's ``add_executable`` command might look like this:

..  code-block:: text

    add_executable(${TARGET_NAME}
        include/someproject/classA.hpp
        include/someproject/classB.hpp
        include/someproject/classC.hpp

        main.cpp
        src/classA.cpp
        src/classB.cpp
        src/classC.cpp
    )

..  note:: Please be sure to list files in alphabetical order, in two groups:
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

..  important:: You **must** save the ``.config`` file in the root of the repository.
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

..  warning:: Our dependency libraries (``libdeps``) are not
    currently configured to switch architectures. You will need to manually
    compile these and point to them using a ``.config`` file.
