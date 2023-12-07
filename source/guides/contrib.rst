..  _gcontrib:

Guide: Contributor Introduction (Start Here!)
###############################################

Welcome to MousePaw Media! We're so excited you've chosen to volunteer
your time and skills to help make our projects awesome.

This tutorial will help you get started as an open source contributor.

GitLab
===============================

Nearly all of our development work takes place on our **GitLab**,
located at `gitlab.mousepawmedia.com <https://gitlab.mousepawmedia.com>`_.
Your MousePaw Media GitLab account also serves as your login for other
parts of our development network.

1.  Log in or create an account now.

2.  Read through and sign the **Community Rules**, **Terms of Development**, and
    **Terms of Use**.

3.  You should now see the home page of our GitLab. Click your profile
    picture at the top of the sidebar, and click :guilabel:`Edit Profile`.
    Fill out as much as you wish, but especially the following.
    Make sure you click :guilabel:`Update profile settings` when you're done.

    - Public avatar
    - Time zone
    - Full name
    - Pronouns
    - Public email

4.  Click :guilabel:`Preferences` on the left sidebar. Here, you may define
    the appearance and behavior of GitLab to match your preferences.

To learn more about how to use GitLab, check out their comprehensive
documentation at `GitLab Docs <https://docs.gitlab.com/>`_. You can acces
this, as well as see the current version of GitLab we are running, by clicking
:guilabel:`Help` in the lower-left corner of GitLab anytime.

..  note:: We are running the Free Self-Hosted tier of GitLab EE, so some
    features are not available to us. In the documentation, take note when
    a feature is indicated as being for the Premium or Ultimate tier or
    SaaS offering.

Discourse
===============================

Most of our collaboration takes place on Discourse. It is home to our
community, as well as Q&A, project discussion, our issue tracker, and
our software product specifications.

You can sign into Discourse at ``https://discourse.mousepawmedia.com``
using your MousePaw Media GitLab account.

Be sure to read the community rules by clicking the :guilabel:`FAQ` button
from the left sidebar.

Reading and Posting
-----------------------------------

When you first go to Discourse, you'll start on the Home screen. This
displays all the categories, as well as the latest posts.

Click the "General" category, and then click the "Personal Introductions" topic.
After reading as much as you want, click the :guilabel:`Reply` button at
the bottom of the topic (*not* the one at the bottom of each post) to create
your own post on the topic.

..  important:: Reply to the "Personal Introductions" topic with an introduction
    of yourself.

You can learn more about using Discourse from the "Discourse New User Guide"
under the "General" category.

..  important:: Read "Discourse New User Guide".

Issue Tracker
-----------------------------------

We are in the process of building our own issue tracker. In the meantime,
Discourse is serving as our issue tracker, and will continue to be the
staging area for community-reported issues.

You can view the issue tracker by visiting the "Issue Tracker" category,
or by clicking :guilabel:`Issue Tracker` on the left sidebar.

If you're looking for easy tasks to start with, look for ones that have
been tagged with low Friction scores (f1 or f2) and low Relativity scores
(r1 or r2). (You can learn more about Friction and Relativity from the
`Quantified Tasks website <http://quantifiedtasks.org/>`_).

Getting Started with Development
===================================

You should start by setting up your :ref:`Development Environment <genv>`.

Each of our projects has a dedicated Git repository. You should create a
dedicated folder on your computer for the repositories you clone. You can find
each repository's clone command on our GitLab. If you need help
with cloning a repository, see :ref:`git_importrepos`.

After you have cloned a project repository, see its :file:`BUILDING.md` file
for instructions on how to build the code for the first time.

For help with working with these repositories and building code, see
:ref:`gbuild`

Submitting the Code
===================================

Once you're ready for the code to be reviewed, you should submit a Merge
Request on the MousePaw Media GitLab. For more information on how to do this,
see :ref:`grevision`.
