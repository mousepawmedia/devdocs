.. _grevision:

Guide: Code Contributions
#####################################

Instead of GitHub Pull Requests, we use Phabricator Differential. Here's
how to get started with one.

.. _grevision_firsttime:

First Time Setup
=========================

The first time you contribute code, you'll want to set up your development
environment. At minimum, you will need to set up :ref:`gitarc`. You may also
need to set up the tools for :ref:`C and C++ <cpp>`, :ref:`Python <python>`,
and/or :ref:`Sphinx <sphinx>`.

You'll also need to :ref:`Import the Repository <gitarc_importrepos>` you'll
be working with.

.. _grevision_gettingready:

Getting Ready
========================

Before you start making changes, you should reference the :ref:`p_workflow`,
so you know what's expected. For each batch of changes, you should plan them
out on Phabricator Maniphest before starting.

..  sidebar:: The Ten Commandments of Git

    #I-X: NEVER WORK ON THE MASTER OR STABLE BRANCH!

Before you make any changes, you should create a new branch on your local
copy of the repository. You should **never** work on ``master``. To create
a new branch, run...

..  code-block:: bash

    $ git checkout -b newbranchname

...where *newbranchname* is the name of the branch you're creating. You can
call it absolutely anything (except "master" or "stable").

.. _grevision_changes:

Making Changes
===========================

Now you can begin working. Every day you finish work, you should **diff** your
changes, meaning you upload them to a Differential Revision. To create a
Revision, or update the one you already have open for the repository, run...

..  code-block:: bash

    $ git add .
    $ git commit
    $ arc diff

The first time you run ``arc diff``, it will bring up a form for you to fill
out to create a new Revision. Give it a brief, descriptive title. Describe
your overall plans in the description.

If you're ready for code review and feedback, add a reviewer. If you don't
know who to add, you can use the Reviewer Finder (see :ref:`phab_reviewerfinder`)
to find someone. You should especially favor tagging someone in the same
:ref:`code territory <p_workflow_planning_territory>` you're working in.

If you're NOT ready for review, don't worry. You can always add a reviewer later.

You should be aware of the :ref:`p_workflow_landing_checklist`, which your
Revision must pass before it can be accepted and **landed**.

.. _grevision_landing:

Landing Changes
========================

Once your Differential Revision has been approved, you can land it, meaning you
push the changes to the ``master`` branch. In the repository, run...

..  code-block:: bash

    $ arc land yourbranchname

...where *yourbranchname* is the name of the branch you've been working in.

Be sure to keep your Maniphest tasks up-to-date, marking them as complete or
updating them as appropriate.

Finally, sit back and relax, knowing you've made a valuable contribution to
MousePaw Media! (When you're done relaxing, feel free to start a new set of
changes.)
