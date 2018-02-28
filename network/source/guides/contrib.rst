..  _gcontrib:

Guide: Contributor Introduction
###########################################

Welcome to MousePaw Media! We're so excited you've chosen to volunteer
your time and skills to help make our projects awesome.

This tutorial will help you get started as an open source contributor.

Logging On
===============================

Phabricator
-------------------------------

Nearly all of our development work takes place on **DevNet Phabricator**,
located at `phabricator.mousepawmedia.net <https://phabricator.mousepawmedia.net>`_.

1. Log into your `GitHub <https://github.com/>`_ account. If you haven't
   already created a GitHub account, do that now.

2. Go to `phabricator.mousepawmedia.net <https://phabricator.mousepawmedia.net>`_
   and click the :guilabel:`Log In or Register (GitHub)` button towards the
   bottom of the screen.

..  NOTE:: The LDAP username and password are for staff only. Don't worry
    about that.

3. Read through and sign the **Community Rules**.

4. You should now see the home page of DevNet Phabricator. Click your profile
   picture on the top bar, towards the right, and select :guilabel:`Settings`.
   Review and change the following *at minimum*.
   Be sure to click :guilabel:`Save Changes` on each page!

* :guilabel:`Account`: Set desired ``Pronoun``.

* :guilabel:`Date and Time`: Set ``Timezone``.

* :guilabel:`Conpherence`: Set ``Notifications`` to ``Send Notifications``.

* :guilabel:`Notifications`: We recommend setting ``Notifications`` to
  ``Web and Desktop``, so you'll get the latest notifications as they happen
  while you're logged onto Phabricator. You may also need to grant permission
  for the desktop notifications to work.

* :guilabel:`Email Addresses`: Ensure your desired email address is added to
  this list, and ``Verify`` it. Verification emails come from
  ``hawksnest@mousepawmedia.com``; you may need to check your spam bin if you
  don't get it.

* :guilabel:`Email Delivery`: Set ``Self Actions`` to ``Disable``. You may also
  choose to disable all email notifications under ``Email Notifications`` if
  you plan to check Phabricator manually. Otherwise, if you only want *some*
  emails, you can select which notifications to receive via email on the
  :guilabel:`Email Preferences` pane.


5. Click your profile picture on the top bar, towards the right, and select
   :guilabel:`Manage`. Click :guilabel:`Edit Profile` at right and fill out
   your user profile.

That's it! You're now set up to use DevNet Phabricator! For complete
instructions on using Phabricator, see :ref:`phab`.

..  WARNING:: DevNet (anything at ``mousepawmedia.net``) is only online from
    6:00am until 10:00pm Pacific time, seven days a week. You can see the
    current status at `mousepawmedia.com/devnet <https://mousepawmedia.com/devnet>`_

Freenode IRC
-------------------------------

We use IRC chat for real-time collaboration. You can find us in the
``#mousepawmedia`` room on the Freenode network. For help setting up and
using IRC, see :ref:`irc`.

Development Environment
===============================

Operating System
-------------------------------

We officially support the following operating systems for development work:

* Debian Linux (recommended)
* Other Linux
* macOS
* FreeBSD
* Windows (support upcoming)

..  NOTE:: We do not provide technical support for third-party development
    tools on non-Debian systems.

Tools
-------------------------------

Before you get started, you should set up your development environment.
You need the following:

* **Git**
* **Arcanist**
* **CMake**

For setup instructions for Git and Arcanist, see :ref:`gitarc`.

For C/C++ development:

* **C/C++ compiler and toolchain**: Clang (recommended), GCC, or
  MSVC (support upcoming).
* **A C++ IDE**: We recommend either VSCode, Code::Blocks, or Atom.

If you're using Linux, see :ref:`cpp`.

For Python development:

* **Python 3**
* **A Python IDE**: We recommend either VSCode or Atom.

If you're using Linux, see :ref:`python`.

For Sphinx development:

* **Python 3**
* **Sphinx**
* **A code editor**: We recommend either VSCode or Atom.

If you're using Linux, see :ref:`sphinx`.

We have guides for setting up :ref:`vscode`, :ref:`codeblocks`, and
:ref:`atom`.

Getting Started
===============================

A Quick Tour of Phabricator
-------------------------------

The first time you log into Phabricator, it can look a bit overwhelming.
Don't panic! There are only a few things you need. We've listed them here
in the relative order they appear on the main menu on the Phabricator
home page (a.k.a. ``Right Now``).

* **Getting Started**: Instructions to get started.
* **Maniphest**: Task and bug tracker. Similar to GitHub Issues.
* **Ponder**: Q&A, StackOverflow style.
* **Phriction**: Our wiki. You are welcome to create and edit pages.
  Similar to GitHub Wiki.
* **Projects**: Browse projects, groups, and other tags. Projects also have
  **Workboards**, similar to GitHub Projects.
* **Differential**: Submit and review code (pre-commit review). Similar to
  GitHub Pull Requests.
* **Audit**: Post-commit review. Raise concerns about code that's already been
  committed.
* **Diffusion**: Browse code and commits. Similar to GitHub Code.
* **Task Finder [P]**: Find a programming task to work on.
* **Reviewer Finder [P]**: Find someone who can review your code.
* **Paste**: Pastebin with syntax highlighting, history, and comments.
  Similar to GitHub Gist.

From anywhere in Phabricator, you can always click the company logo in the
upper left corner of the page to go Home.

Finding a Task
-------------------------------

You may already know what you want to work on. If you don't, you can use
our **Task Finder** to locate one. See :ref:`gtaskfinder` to learn how to use
that feature.
