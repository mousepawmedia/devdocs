.. _grevision:

Guide: Code Contributions
#####################################

Instead of GitHub Pull Requests, we use Phabricator Differential. Here's
how to get started with one.

.. _grevision_firsttime:

First Time Setup
=========================

The first time you work with our repositories code, you'll want to
:ref:`set up your development environment <genv>`. You'll also need to
read :ref:`gbuild`.

.. _grevision_gettingready:

Getting Ready
========================

Before you start making changes, you should reference the :ref:`p_workflow`,
so you know what's expected. For each batch of changes, you should plan them
out on Phabricator Maniphest before starting.

..  sidebar:: The Ten Commandments of Git

    #I-X: NEVER WORK ON THE DEVEL, FRESH, OR STABLE BRANCH!

Before you make any changes, you should create a new branch on your local
copy of the repository. You should **never** work on ` `devel` `. To create
a new branch, run...

..  code-block:: bash

    $ git checkout -b newbranchname

...where *newbranchname* is the name of the branch you're creating. You can
call it absolutely anything (except "devel", "fresh", or "stable").

.. _grevision_changes:

Making Changes
===========================

..  sidebar:: **In-Progress Differentials**

    We consider it good practice to create/update your Differential Revision
    after *every work session*. This stores the code remotely (in case
    something terrible happens), and allows you to get feedback on your work
    so far. To do this, submit via :code:`arc diff` like normal. Then on your
    Differential Revision, scroll to the bottom, click :guilabel:`Actions...`,
    select :guilabel:`Plan Changes`, and press :guilabel:`Submit`. Easy as that!

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
push the changes to the ` `devel` ` branch. In the repository, run...

..  code-block:: bash

    $ arc land yourbranchname

...where *yourbranchname* is the name of the branch you've been working in.

Be sure to keep your Maniphest tasks up-to-date, marking them as complete or
updating them as appropriate.

Finally, sit back and relax, knowing you've made a valuable contribution to
MousePaw Media! (When you're done relaxing, feel free to start a new set of
changes.)

.. _grevision_patch:

Pulling Down A Revision
==========================

If you want to pull down someone else's Differential Revision to your local
repository for testing it out, or if you want to recover your lost work
from your Differential Revision, Arcanist can do that!

First, make sure you're on the devel branch, and it is up-to-date.

..  code-block:: bash

    $ git checkout devel
    $ git pull origin devel

Then patch the Differential Revision with...

..  code-block:: bash

    $ arc patch D###

...where :code:`D###` is the Differential Revision ID (such as ``D123``).

This will create a new branch called ``arcpatch_D###``. You may want to take
note of the original branch name mentioned on the Differential Revision page,
and rename the ``arcpatch_D###`` branch to the correct name. You can do this
via...

..  code-block:: bash

    $ git branch -m arcpatch_D### newbranchname
