.. _grevision:

Guide: Code Contributions
#####################################

We use GitLab Merge Requests to propose, review, and approve code changes.

.. _grevision_firsttime:

First Time Setup
=========================

The first time you work with our repositories code, you'll want to
:ref:`set up your development environment <genv>`. You'll also need to
read :ref:`gbuild`.

.. _grevision_gettingready:

Getting Ready
========================

..  sidebar:: The Ten Commandments of Git

    #I-X: NEVER WORK ON THE DEVEL, FRESH, OR STABLE BRANCH!

Before you make any changes, you should create a new branch on your local
copy of the repository. You should **never** work on :code:`devel`. To create
a new branch, run...

..  code-block:: bash

    git checkout -b newbranchname

...where *newbranchname* is the name of the branch you're creating. You can
call it absolutely anything (except "devel", "fresh", or "stable").

.. _grevision_changes:

Making Changes
===========================

Every day you finish work, you should commit and push your changes to a remote
branch (NOT *devel*, *fresh*, or *stable*). To do this, run:

..  code-block:: bash

    git add .
    git commit
    git push origin newbranchname

If you're ready for code review and feedback, go to GitLab and create a
Merge Request. You may consider nominating a reviewer, although this is
optional. If you're NOT ready for review, click :guilabel:`Mark as draft`
on the Merge Request.

Learn more about creating Merge Requests here: `https://docs.gitlab.com/ee/user/project/merge_requests/creating_merge_requests.html <https://docs.gitlab.com/ee/user/project/merge_requests/creating_merge_requests.html>`_

Once your Merge Request has passed all CI/CD checks and been approved, any
Developer on the project can merge it to the :code:`devel` using the button on
the Merge Request.

.. _grevision_patch:

Pulling Down a Merge Request
================================

If you want to pull down someone else's Merge Request to your local
repository for testing it out, or if you want to recover your lost work
from your Merge Request, just fetch the associated branch.

..  code-block:: bash

    git fetch origin
    git checkout nameofthebranch

...where *nameofthebranch* is the name of the branch you want to clone.
