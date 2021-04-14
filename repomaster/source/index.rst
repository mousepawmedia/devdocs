.. MousePaw Media Development Documentation master file, created by
   sphinx-quickstart on Mon May 30 15:25:19 2016.
   You can adapt this file completely to your liking, but it should at least
   contain the root `toctree` directive.

MousePaw Media Repository Master Manual
##########################################

About This Manual
===================================
To ensure the highest code quality and simplest workflows, we have a carefully
developed and curated code management and build system. We've written thorough
documentation, to ensure that staff is able to manage and maintain this system.

As of 2018, all programming staff will be required to pass Repository Master
training, and it will become a mandatory part of a programming internship. The
documentation provided here contains all the information necessary to pass
MousePaw Media's Repository Master Certification Exam [RMCE].

What Is a Repository Master?
-----------------------------------
A **Repository Master** is someone with elevated access and control privileges
over the repositories and build systems. They must have an up-to-date
Repository Master certification from MousePaw Media.

A Repository Master is responsible to diagnose and fix problems with the
:abbr:`VCS (version control software, e.g. git)`, build systems, mirrors, and
:abbr:`CI (continuous integration, e.g. Jenkins)` system. They are also
responsible for ensuring compliance with policies and coding standards.
Finally, they help minimize bugs and prevent breakages and substandard code
in protected branches (``devel``, ``fresh``, and ``stable``) and release tags.

Although the majority of programming staff members have proper certification,
only one or two individuals are granted the Repository Master role at any one
time. This ensures that policies are properly enforced, and simplifies the
process by which problems are resolved.

Preparing For The RMCE
-----------------------------------
As of 2018, MousePaw Media programming staff must pass the
:abbr:`RMCE (Repository Master Certification Exam)` before acting as a
Repository Master. This exam covers the following topics:

* Git VCS

* Jenkins CI

* Phabricator's Git and Jenkins integrations

* Our in-house build system, including...

  * CMake

  * The Clang and GCC compilers

  * Makefiles

* Static analysis tools

* Dynamic analysis tools

* Debugging tools

* Testing

* MousePaw Media Standards

* VCS policies

..  note:: The RMCE only covers relevent topics in such vast areas as Git,
    Jenkins, CMake, and Clang. Further independent study is encouraged.

The exam is open-book, and will focus primarily on your ability to recognize
errors and resolve problems. None of the questions will be multiple choice. Be
prepared to write commands free-form, correct configuration files, and detail
the steps necessary to fix stated problems.

Using This Text
-----------------------------------

If you are preparing for the RMCE, you should read everything presented here
*in order*. Concepts build on each other, and we will assume you've read
prior sections.

You should also **read source material**. We are doing our best to summarize,
but further useful information can be found on our source links. Thus, you
should **thoroughly read the material on source links**, with one exception.
If a source link has an asterisk at the beginning
(i.e. `*Some Huge Documentation (Example)`), this means you don't have to
read through the link, as it is likely exhaustive. However, in that case, you
should still skim it, and bookmark the source for further reference.

Table of Contents
===================================

..  toctree::
    :maxdepth: 2

    buildsystem/c_and_cpp
    buildsystem/python
    buildsystem/sphinx

    repository/git
    repository/arcanist
    repository/phab
    repository/github

    infrastructure/debugging
    infrastructure/staticanalysis
    infrastructure/dynamicanalysis
    infrastructure/testing
    infrastructure/ci

Indices and tables
===================================

..  toctree::
    :maxdepth: 1

    glossary

* :ref:`genindex`
* :ref:`search`

Feedback
===================================
This documentation is written and maintained by the MousePaw Media Standards
Board. Feedback is welcome via email (developers@mousepawmedia.com).

You can also frequently find us in the #mousepawmedia channel on Freenode.

Pull requests to this repository are not accepted. If you wish to propose a
change, email your patch to us at the address above.

For more information about contributing to MousePaw Media
projects, see `mousepawmedia.com/developers <https://www.mousepawmedia.com/developers>`_.
