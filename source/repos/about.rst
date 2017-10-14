..  _rmdabout:

Repository Master Documentation
###################################

About This Documentation
===========================
To ensure the highest code quality and simplest workflows, we have a carefully
developed and curated code management and build system. We've written thorough
documentation, to ensure that staff is able to manage and maintain this system.

As of 2018, all programming staff will be required to pass Repository Master
training, and it will become a mandatory part of a programming internship. The
documentation provided here contains all the information necessary to pass
MousePaw Media's Repository Master Certification Exam [RMCE].

What Is a Repository Master?
=================================
A **Repository Master** is someone with elevated access and control privileges
over the repositories and build systems. They must have an up-to-date
Repository Master certification from MousePaw Media.

A Repository Master is responsible to diagnose and fix problems with the
:abbr:`VCS (version control software, e.g. git)`, build systems, mirrors, and
:abbr:`CI (continuous integration, e.g. Jenkins)` system. They are also
responsible for ensuring compliance with policies and coding standards.
Finally, they help minimize bugs and prevent breakages and substandard code
in protected branches (e.g. `master` and `stable`) and release tags.

Although the majority of programming staff members have proper certification,
only one or two individuals are granted the Repository Master role at any one
time. This ensures that policies are properly enforced, and simplifies the
process by which problems are resolved.

Preparing For The RMCE
===================================
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

* Goldilocks testing

* Static analysis tools

* MousePaw Media Standards

* VCS policies

..  NOTE:: The RMCE only covers relevent topics in such vast areas as Git,
    Jenkins, CMake, and Clang. Further independent study is encouraged.

The exam is open-book, and will focus primarily on your ability to recognize
errors and resolve problems. None of the questions will be multiple choice. Be
prepared to write commands free-form, correct configuration files, and detail
the steps necessary to fix stated problems.
