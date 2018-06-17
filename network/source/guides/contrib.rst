..  _gcontrib:

Guide: Contributor Introduction (Start Here!)
###############################################

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

You should start by setting up your :ref:`Development Environment <genv>`.

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

Getting the Code
-------------------------------

Each of our projects has a dedicated Git repository. You should create a
dedicated folder on your computer for the repositories you clone. You can find
each repository's clone command on Phabricator Diffusion. If you need help
with cloning a repository, see :ref:`gitarc_importrepos`.

After you have cloned a project repository, see its :file:`BUILDING.md` file
for instructions on how to build the code for the first time.

For our C++ projects, you will almost certainly need the following:

* :code:`libdeps`: All our third-party dependency libraries.

* :code:`pawlib`: Our in-house utility library.

For help with working with these repositories and building code, see
:ref:`gbuild`

Finding a Task
-------------------------------

You may already know what you want to work on. If you don't, you can use
our **Task Finder** to locate one. See :ref:`gtaskfinder` to learn how to use
that feature.

Once you've found the task you want to work on, scroll to the bottom of its
page, to the comment box. From :guilabel:`Actions...`, select
:guilabel:`Assign/Claim`, and make sure your name is selected.

Submitting the Code
-------------------------------

Once you're ready for the code to be reviewed, you should submit it to a
Phabricator Differential.

If you have set up Arcanist (see :ref:`gitarc`), you can use it to create a new
Differential Revision, or update the one you already have open. For more
information on how to do this, see :ref:`grevision`.