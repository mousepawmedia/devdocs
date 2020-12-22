..  _p_workflow:

Programming Workflow
#################################

Purpose
=================================

To maintain productivity and keep our code high-quality, we use the following
programming workflow.

While this bears some resemblance to aspects of Agile methodology, the
similarities are purely coincidental. We do not subscribe to any particular
methodology. Our workflow will be continually revisited and revised to ensure
that it is effective and helpful to us.

There are three major stages in our programming workflow:

1) Planning

2) Building

3) Landing

..  _p_workflow_planning:

Step One: Planning
==========================

Before coding anything, you should map out all of the major features and
improvements you have planned for your patch or new version.

..  IMPORTANT:: Should you create a task, or no? Here's the general rule: If the
    task will take less than 10 minutes, and you're going to do it right now,
    you do NOT have to create a task. Otherwise, *create a task*!

See :ref:`gtaskcreate_taskorproject`.

Steps in Planning
---------------------------

1)  Create a subproject or umbrella task.

2)  Add tasks to that subproject/umbrella task for each feature and improvement.

3)  Ensure each task has...

    * A description describing the goal. You can generally include details on
      what has to be done, and how to potentially do it.

    * Tags for project, department, and team.

    * Set QTM measures: priority, gravity, friction, and relativity. (Gravity
      Points should match Gravity).

4)  Use Maniphest comments to discuss the task with others (and your future
    self), as necessary. For example...

    * Tracking how you fixed a bug.

    * Brainstorming feature details and implementation over time.

    * Discussing and tracking changes in project design.

..  _p_workflow_planning_territory:

Code Territory
---------------------------------

Whenever working on a section of code, you should be aware of what
**code territory** you're in. Different teams are in charge of different
sections of the code, based on their skills and experience.

When making a revision to a sector of code, you should have the code reviewed
by an active member of the team whose code territory you're working in.
If you aren't sure who to ask for a review, set the Team Lead as the reviewer.
They will reassign as necessary.

You can find both the current Team Lead and the Code Territory lists on the
each team page.

`A-Team <https://phabricator.mousepawmedia.net/project/view/35/>`_: Integration

`Black Sheep Squadron <https://phabricator.mousepawmedia.net/project/view/25/>`_: Data

`Impossible Missions Force <https://phabricator.mousepawmedia.net/project/view/24/>`_: Multimedia

See :ref:`phab_projects_types`.

..  _p_workflow_building:

Step Two: Building
==========================

Repository Structure
--------------------------

Most repositories follow the same basic structure.

* :file:`docs/` ← Contains the Sphinx documentation.

  * :file:`source/` ← Source files for documentation.

  * :file:`build/` ← Compiled documentation files; automatically generated.

  * :file:`Makefile` ← Sphinx Makefile

* :file:`application/` ← Only for application repositories.

  * :file:`include/` ← Header files (if applicable).

  * :file:`src/` ← Source files.

  * :file:`CMakeLists.txt` ← CMake build instructions for application.

  * :file:`Makefile` ← Application makefile.

* :file:`library/` ← Only for static library repositories.

  * :file:`include/` ← Header files (if applicable).

  * :file:`src/` ← Source files.

  * :file:`CMakeLists.txt` ← CMake build instructions for library.

  * :file:`Makefile` ← Library makefile.

* :file:`tester/` ← Only for static library repositories.

  * :file:`include/` ← Header files (if applicable).

  * :file:`src/` ← Source files.

  * :file:`CMakeLists.txt` ← CMake build instructions for testing application.

  * :file:`Makefile` ← Testing application makefile.

* :file:`{thirdparty}/` ← A top-level directory exists for each third-party
  library we include. Each would have the library's name, such as :file:`cpgf/`.

* :file:`{compiled}/` ← Has project name. :code:`make` automatically creates
  this directory and copies the distribution files to it.

* :file:`BUILDING.md` ← Contains building instructions and notes.

* :file:`LICENSE.md` ← Contains all the license data for the entire project.

* :file:`README.md` ← Contains description, credits, and development notes.

* :file:`Makefile` ← Master project makefile.

Build System
--------------------------------

Our C and C++ projects use CMake for the build system. The Makefiles and
Visual Studio Code projects are configured to use the CMake system.

This build system *should* work out of the box, so long as you follow a few
simple rules.

Including Files
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

First, ensure all code files (``.h``, ``.c``, ``.hpp``, ``.cpp``, etc.) are
included in the appropriate :file:`CMakeLists.txt` file. Open that file and
scroll down to the section where all of the source code file paths are listed,
and add the new paths. Be sure to follow alphabetical order.

External Dependencies
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Second, ensure all external dependencies are where the build system expects
them. We configure all of our repositories to look for their external
dependencies in a repository folder *next to* the repository you're in.

For example, PuppeteerXML externally depends on PawLIB. The build system for
PuppeteerXML expects a folder structure like this...

* :file:`puppeteerxml-git/` ← We're calling :code:`make` from here.

* :file:`pawlib-git/`

  * :file:`pawlib/`

    * :file:`include/`

      * :file:`{headerfile}.hpp` ← These are the header files.

    * :file:`lib/`

      * :file:`libcpgf.a`

      * :file:`libpawlib.a`

In other words, from the root of PuppeteerXML's repository, its build system
will look for the static library file for PawLIB at
:file:`../pawlib-git/pawlib/lib/libpawlib.a`.

If you must use a different structure, see the :file:`build.config.txt` file
in the root of the repository for instructions on how to make the build system
look for the external dependencies elsewhere.

Compiler and Environment
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

..  sidebar:: What About MSVC?

    We are currently working on adding support for the Microsoft Visual
    compilers. In the meantime, on Windows, you can use MinGW or Cygwin
    for compiling. Otherwise, you're on your own for the moment.

CMake is configured to require a very specific build environment. Our C++
libraries are designed to be compiled on GCC 5 using C++14. Any compiler which
can meet that requirement (including by compiler simulation) should work,
but all others will be blocked.

Our development takes place principally on Debian Linux, and we know our
libraries and applications compile there. However, we haven't yet started
testing compilation on other platforms.

Steps in Building
--------------------------

1)  Make sure :code:`devel` is up-to-date, and then create a new Git branch
    for your work. For example, on the root directory of the repository, on
    the devel branch...

..  code-block:: bash

    $ git pull
    $ git checkout -b newbranch

2)   Pick **one** single feature or bugfix, and start building it.

3)  Add subtasks to your Maniphest task as needed, detailing other features
    and bugfixes necessary to implement the feature.

4)  `CSI <https://standards.mousepawmedia.com/qtm.html>`_ comment as you go!

5)  When practical, update the documentation as you go. It's okay to leave
    gaps in the documentation temporarily, during development.

6)  Keep an eye on Maniphest for bug reports from other developers. Prioritize
    those and bugfix/optimize at will.

7)  Submit your changes to a Revision, updating it *every day you work*.

8)  Link your Maniphest tasks to your Revision.

..  _p_workflow_landing:

Step Three: Landing
==========================

..  _p_workflow_landing_checklist:

Revision Checklist
--------------------------

The `Revision Checklist (P1) <https://phabricator.mousepawmedia.net/P1>`__, which is also described below, lists the
expectations for a completed Differential Revision.

Before landing, each Revision should...

1)  Accomplish the feature(s) it was designed to accomplish. [In some cases,
    the feature itself may be dropped, and only bugfixes and/or optimizations
    landed instead.]

2)  Have merged all changes from :code:`devel` into itself, and all conflicts
    resolved. (:code:`$ git pull origin devel`).

3)  Have binaries and unnecessary cruft untracked and removed. (Keep an eye on
    .gitignore!)

4)  Compile and run properly - this should be confirmed via Harbormaster/Jenkins
    (if available).

5)  Be free of compiler errors and warnings. Must compile with
    :code:`-Wall -Wextra -Werror`.

6)  Be Valgrind pure (no memory leaks detected).

7)  Comply with Coding and Technical standards.

8)  Be free of linter errors. (:code:`$arc lint --lintall`)

9)  Be fully CSI commented.

10) Have an up-to-date build script (generally CMake) if relevant.

11) Contain relevant LIT tests, if the project is Goldilocks capable.

12) Have a Test Plan, generally containing a list of Goldilocks tests the
    reviewer should run.

13) Be reviewed, built, tested, and approved by at least one trusted reviewer
    (Staff or Trusted Contributor).

14) Have up-to-date Sphinx documentation, which compiles with no warnings.

15) Have all reviewer comments processed and marked as "Done".

Don't worry! Although the list looks long, you can stay on top of most of these
items as you code, while some others require minimal effort.

Linting
------------------------------------

When you update a Revision, Arcanist automatically runs the ``cppcheck``
linter on C++ code. The result of this linter is posted on your Revision. It
must pass before the code can be landed.

Unfortunately, our Python 3 linter (``pylint3``) is not yet configured to work
with Arcanist. This linter will need to be run manually.

Automatic Build and Test System
------------------------------------

Phabricator monitors certain files, directories, and repositories. If a
Revision or a Commit touches a monitored path, Phabricator automatically
triggers one or more Jenkins builds via the Harbormaster application. The build
result is reported back to the Revision or Commit.

Our C++ projects use the Goldilocks library for testing and benchmarking.
Our Jenkins instance is configured to use these Goldilocks tests.

..  NOTE:: To learn more about setting up and using Goldilocks, see the
    `PawLIB Goldilocks documentation <https://mousepawmedia.net/docs/pawlib/goldilocks/goldilocks.html>`_.

If a build or test suite fails, Jenkins will report the failure status and
provide a link to the full build results.

(See :ref:`harbormasterjenkins`)

Controlling Automatic Builds
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Sometimes, you may want to update a Revision without the automatic builds or
tests running. You can temporarily shut these off using two special tags.

By including the tag :code:`#nobuild` under Subscribers, all automatic builds
will be skipped. If you only want to skip the tests, include the tag
:code:`#notest`.

..  WARNING:: Be sure to remove these tags from Subscribers *before* your final
    Revision update, to ensure the appropriate builds and tests are run.
    You can't land the Revision until they are!
