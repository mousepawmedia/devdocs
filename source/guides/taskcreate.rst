.. _gtaskcreate:

Guide: Task Creation
######################

..  sidebar:: Rule Of Tasks

    If the goal won't be accomplished in the next 10 minutes, *make a task*.

Task management is central to every workflow at MousePaw Media, across all
departments. We use Phabricator Maniphest for this. (See :ref:`phab_maniphest`).

.. _gtaskcreate_creating:

Creating Tasks
==============================

.. _gtaskcreate_creating_report:

Creating a Bug Report/Feature Request
-------------------------------------------

If you're reporting a bug or requesting a feature on a project you aren't
directly involved in developing, you should use the Bug Report or Feature
Request form. On Maniphest, click the :guilabel:`Create Task` button at
upper right, and select one of these two options:

* :guilabel:`Bug Report` creates a new Bug Report for a project you aren't
  working on. It only prompts you for the essential information, and leaves the
  actual prioritization to the person working on that bug.

* :guilabel:`Feature Request` is like Bug Report, but is focused on a requested
  feature instead of an actual bug.

Fill out the following information...

* Title: Describe the bug or feature succinctly. Include the name of the
  code sector. (See :ref:`gtaskcreate_practice_titles`).

* Proposed Urgency: This is how urgent you believe the task is. Actual
  priority and gravity will be determined by a project member later.

* Task Type: This is automatically set to Bug or Feature, depending on which
  form you're using.

* Description: Describe the bug or requested feature in detail.
  (See :ref:`gtaskcreate_practice_description`).

* Visible To: This should generally be left on the default visibility
  (Global, All Users), unless you're a staff member and you need to hide
  an internal task from outside contributors.

* Editable By: Changes are tracked, so you *can* leave this set to All Users.
  At minimum, all staff should be able to edit.

* Tags: You **must** include at least one project, one department, and (for
  programming tasks) the team that the project belongs to.
  (See :ref:`gtaskcreate_practice_tagging`).

* Subscribers: If you know of a particular user or users who should be aware
  of the task, or who you want input from, include them in Subscribers.

* Energy Points: If you see this box, you can leave it blank. It will be
  filled in when the task is triaged.

After filling out the form, click :guilabel:`Create New Task` to submit it.
It will live in the Triage queue until it can be processed.

.. _gtaskcreate_creating_task:

Creating a Task
--------------------------------------------

If you're creating a task for a project you're a part of, you'll usually
create either a User Story, a Spike, or an Epic. You can find these on
Maniphest, by clicking :guilabel:`Create Task` in the upper-right corner,
and then selecting :guilabel:`User Story`, :guilabel:`Epic`, or
:guilabel:`Spike` from the menu.

Which type of task you select matters:

* A :guilabel:`User Story` is a single, well-defined goal for a project.
  It is so-named because you define a brief summary of the goal from the
  perspective of the user you have in mind, often in the form of
  "As an <sort of user>, I want to <do a thing> because <reason>."
  You usually only create User Stories for a project *you* are working on.

* A :guilabel:`Spike` is a task in which you have a limited, defined period
  of time in which to research something or experiment with an idea.

* An :guilabel:`Epic` is a collection of User Stories with a cohesive
  overall goal. You usually only create Epics for a project *you* are
  working on.

* :guilabel:`D+P Task` is for Design+Production department tasks.

* :guilabel:`Private Task` is for any task that should be hidden from everyone
  but you. Please only use this if the task is MousePaw Media-related, but
  unrelated to an actual project. For example, you may create an "Organize
  Work Inbox" task, or a "Read 'Dreaming in Code'" task - those sorts of tasks
  only matter to you, and might even overlap a similar task for another user
  if made public.

..  warning:: DO NOT use Private Task for *any* project-related task!

Fill out the following fields:

* Title: Describe the task succinctly. Include the name of the
  code sector. (See :ref:`gtaskcreate_practice_titles`).

* Task Type: There are seven types of task. You should select one.

  * Bug: An error or misbehavior that should be fixed.

  * Feature: A new behavior or capability that should be added.

  * Improvement: Anything that makes the existing behavior or capability
    function *better*.

  * Design: Tasks that don't actually involve making changes to the project,
    but are related to planning changes.

  * Documentation: Tasks related to writing the comments and documentation
    for the project, but not otherwise changing the project.

  * Research: Tasks related to researching or experimenting with something.

  * Other: Anything and everything else.

..  sidebar:: Where Is WONTFIX?

    The common bug status "WONTFIX" is actually counter-intuitive. It tells
    outsiders "we know the bug exists, and don't plan to do anything about
    it," effectively damaging the development team's reputation by making
    them look lazy and complacent.

    We use the terms "Invalid" and "Rejected" in place of "WONTFIX", which
    separates a bug report rejection into two categories: the bug never
    existed (invalid) and the bug cannot be fixed (rejected).

* Status: A task can have one of several statuses. The default is "Open".
  (See :ref:`gtaskcreate_practice_status`).

* Assigned To: A single user who should work on this task. If you don't
  know who to assign a task to, just leave this blank.

* Description: Describe the bug or requested feature in detail.
  (See :ref:`gtaskcreate_practice_description`).

* Tags: You **must** include at least one project, one department, and (for
  programming tasks) the team that the project belongs to.
  (See :ref:`gtaskcreate_practice_tagging`).

* Proposed Urgency: This is set to "Established" by default, unless it was
  set by the original bug report/feature request creator. It remains locked
  for reference purposes.

* Priority: How soon should the task be completed?
  (See `QTM: Priority <https://standards.mousepawmedia.com/en/devel/qtm.html#priority>`_)

* Gravity: How important is the task to the project's overall goals and stability?
  (See `QTM: Gravity <https://standards.mousepawmedia.com/en/devel/qtm.html#gravity-importance>`_)

* Distance: How long the task would take a developer, given full domain knowledge?
  (See `QTM: Distance <https://standards.mousepawmedia.com/en/devel/qtm.html#distance-effort>`_)

* Friction: How many resources are available to help one in completing this task?
  (See `QTM: Friction <https://standards.mousepawmedia.com/en/devel/qtm.html#friction-available-help>`_)

* Relativity: How certain are you that the task can be completed by the given
  completion date or in the given number of hours?
  (See `QTM: Relativity <https://standards.mousepawmedia.com/en/devel/qtm.html#relativity-uncertainty>`_)

* Energy Points: This should be calculated according to the formula
  *Distance + Friction x Relativity*.
  (See `QTM: Energy Points <https://standards.mousepawmedia.com/en/devel/qtm.html#energy-points>`_)

* Target Completion Date: When should we aim to have the task completed by?

* Timebox: Time limit for working on task.

* Volatility (Caught At): For bugs *only*, you should indicate what stage you
  found the bug at.
  (See `QTM: Volatility <https://standards.mousepawmedia.com/en/devel/qtm.html#volatility>`_)

* Origin: For bugs *only*, you should indicate what stage the bug originated
  at.
  (See `QTM: Volatility <https://standards.mousepawmedia.com/en/devel/qtm.html#volatility>`_)

* Subscribers: If you know of a particular user or users who should be aware
  of the task, or who you want input from, include them in Subscribers.

* Visible To: This should generally be left on the default visibility
  (Global, All Users), unless you're a staff member and you need to hide
  an internal task from outside contributors.

* Editable By: Changes are tracked, so you *can* leave this set to All Users.
  At minimum, all staff should be able to edit.

After filling out the form, click :guilabel:`Create New Task` to submit it.

.. _gtaskcreate_practice:

Good Task Creation Practice
====================================

.. _gtaskcreate_practice_titles:

Titles
-------------------------------------

This should describe the bug/feature succinctly in 3-10 words, and generally
include the name of the code sector. A title needs to stand alone in describing
the task, bug, or feature request.

Avoid vague titles like "iochannel display bug" or "program not working". Also
avoid overly detailed titles like "flexarray has a segfault when more than
25,000 integers are stored in it".

Here are examples of some *good* titles:

* Allow Override of `ioc` in Goldilocks.

* OneString Code Refactoring: Improving to beat std::string

* [IOChannel] Rework how formatting flags are used and stored

* Blueshell: Cursor Navigation

* Strings not properly persisted in Punchline

.. _gtaskcreate_practice_description:

Description
------------------------------------

For bugs, you should include the following information in the description:

* Exact actions, test name and scenario, or code for duplicating the bug.

* A detailed description of the exact bug. This includes compiler warnings,
  terminal output, Valgrind output, crash reports, and all other such
  information.

* Your development environment; at the minimum, include your operating system.
  If you are staff, you are expected to be running the company's standardized
  development environment. Otherwise, you should include your exact compiler
  version, linked library versions, compiler settings, and all other relevant
  development data.

For features, you should include the following information in the description:

* What is the exact feature or behavior required?

* One or more example use cases for the feature, including sample expected
  input and output.

* An explanation of why the feature is needed. This is important to
  prioritization. "It would be nice if..." takes a backseat to "This common
  scenario doesn't work without...".

* Any initial ideas you have on implementation (if applicable).

.. _gtaskcreate_practice_tagging:

Tagging
--------------------------------

Tagging is very important for project management on Phabricator.

* Each task should have at least one **Project** tag.

* Each task MUST have at least one **Department** tag. This is usually
  either Programming, Content Development, UI Design, or Graphic Design.

* It can be helpful to include one or more **Label** tags, as appropriate to
  the task.

.. _gtaskcreate_practice_magictags:

Magic Tags
----------------------------------

There are two special types of tags related to the Task Finder
(see :ref:`gtaskfinder`): ``Help Wanted`` and ``Low-Hanging Fruit``.

Any task that is fairly brief and straight-forward, and thus could be
completed by a new outside contributor, should be tagged as
``Low-Hanging Fruit``. This automatically places the task in that
section of the Task Finder.

Tasks which are not necessarily Low-Hanging Fruit, but still may be
appropriate for an outside contributor, should be tagged with ``Help Wanted``.
This automatically places the task in the Help Wanted section of the
Task Finder.

While it is perfectly acceptable to tag something with *both* tags, it is
not necessary. The Help Wanted section does not contain any Low-Hanging Fruit.
Conversely, the Low-Hanging Fruit section ignores the Help Wanted tag.

.. _gtaskcreate_practice_status:

Status
-----------------------------------

A task's status should always be kept up-to-date by the task's assignee.

Tasks can have one of many statuses:

* Open: The task is ready to be worked on, possibly barring blocking tasks.

* Use The Force: Same as "Open", but the task is likely to be difficult.
  Also automatically places the task in the Task Finder
  (see :ref:`gtaskfinder`).

* Proposed: We don't know whether the task will actually be worked on.

* On Hold: We stopped working on the task, and don't know whether we will
  resume work.

* Pending Help: All work has stopped on the task, pending help from someone
  else other than the assignee.

* Pending Review: The task is technically done, but outside review is
  required before it can be marked as completed.

* Resolved: The bug has been fixed.

* Completed: The feature/improvement/design/documentation task is finished.

* Patched: The task was temporarily completed, but not truly resolved. For
  example, a bug might have a workaround, but the problem wasn't actually
  solved. This automatically places the task in the Task Finder
  (see :ref:`gtaskfinder`).

* Rejected: We decided not to work on this task.

* Invalid: The task wasn't real. One example is filing a bug report, and then
  discovering the "bug" was actually a user error.

* Slain: We finished this difficult task, and deserve some applause!
