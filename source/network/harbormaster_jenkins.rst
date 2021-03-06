.. _harbormasterjenkins:

Harbormaster and Jenkins
#################################

We use Jenkins for our continuous integration. It automatically runs builds and
tests on code revisions, and it keeps our live documentation up-to-date. This
system is integrated directly into :doc:`/network/phabricator`.

.. _harbormasterjenkins_phab:

Phabricator
==================================

.. _harbormasterjenkins_phab_harbormaster:

Harbormaster
------------------------------------

Phabricator Harbormaster interfaces directly with Jenkins, starting builds and
reporting the status to the rest of Phabricator. You will rarely need to
interact directly with Harbormaster - the build success or failure is usually
posted directly on the Differential Revision or Commit.

.. _harbormasterjenkins_phab_owners:

Owners
-----------------------------------

We use Phabricator Owner to track **Packages** - sets of files, usually relating
to a single library or feature within that library.

When a revision is made to a particular Package, through either a new
Revision or Commit, the related build will be triggered (if applicable).

..  note:: A particular file may belong to more than one Package, and thus may trigger
    more than one build.

.. _harbormasterjenkins_phab_control:

Controlling Builds
----------------------------------

Sometimes you may not want to run builds and/or tests on a particular
Revision yet. If you add the tags ``#nobuild`` and/or ``#notest`` to the
Subscribers field of the Revision, the build will not be triggered.

..  important:: Be aware that you *cannot* land a Revision until the tests have
    run. Thus, you'll want to ensure that you remove ``#nobuild`` and
    ``#notest`` from the Revision before your last update.

.. _harbormasterjenkins_phab_repomasters:

Repository Masters
---------------------------------

A **Repository Master** is a Phabricator user with special privileges on the repositories.
A Repository Master can...

* Land any Revision, regardless of build status.

* Manually start builds.

* Add and modify the build triggers.

* Push to repositories without an accepted Revision.

..  note:: We do not accept requests to become a Repository Master, so don't ask.

.. _harbormasterjenkins_jenkins:

Jenkins
================================

.. _harbormasterjenkins_jenkins_access:

Access and Login
--------------------------

Jenkins is open to the public, in that anyone can access it to view status and
details about builds.

However, only MousePaw Media staff may log into Jenkins using their LDAP
credentials (username and password). Thus, only staff can view build
configurations. Permissions further depend on your role - only Repository
Masters can edit build configurations.

If you're a staff member, and you are having trouble logging in with your LDAP
credentials, contact IT to have your password reset.

..  important:: We do not issue LDAP credentials to non-staff under
    any circumstances.

.. _harbormasterjenkins_jenkins_buildtypes:

Types of Builds
------------------------------

.. _harbormasterjenkins_jenkins_buildtypes_build:

Build
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Most of our projects have a "build" job. These are all configured to work
directly with Phabricator Revisions and Commits, and to run Goldilocks tests
on demand (if the code supports them).

.. _harbormasterjenkins_jenkins_technical:

Technical Details
----------------------------

All C++ builds take place on Docker images with the latest version of our
technical stack, and up-to-date clones of dependencies.
