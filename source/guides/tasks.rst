.. _gtasks:

Guide: Task Creation
######################

..  sidebar:: Rule Of Tasks

    If the goal won't be accomplished in the next 10 minutes, *make a task*.

Task management is central to every workflow at MousePaw Media, across all
departments. We use Phabricator Maniphest for this. (See :ref:`phab_maniphest`).

.. _gtasks_taskorproject:

Task or Project?
===========================================

It can be confusing to determine whether to create a Project, Subproject,
Milestone, Umbrella Task, or Task. Here's the use for each:

* A **Project** is a single library or application. It has multiple versions.

* A **Milestone** is a single version of a Project.

* A **Subproject** is a *subset* of features within a Project. It also can have
  multiple versions.

* An **Umbrella Task** is used for a *single* feature or bugfix that contains
  many smaller tasks. It does NOT have multiple versions; when it's done, it's
  done.

* A **Task** describes a single goal, such as a feature, a bugfix, or an
  optimization task.

To put that all another way, here's a brief rule of thumb:

+-------------------------------------------+----------------+----------------------------------+
| For a...                                  | Use...         | Example                          |
+===========================================+================+==================================+
| Single goal.                              | Task           | "Blueshell: Arguments" {T967}    |
+-------------------------------------------+----------------+----------------------------------+
| Single feature with subtasks.             | Umbrella Task  | "Goldilocks CLI" {T947}          |
+-------------------------------------------+----------------+----------------------------------+
| Large, distinct collection of features.   | Subproject     | "Blueshell [PawLIB]" #blueshell  |
+-------------------------------------------+----------------+----------------------------------+
| Project/Subproject version.               | Milestone      | "PawLIB 1.0" #pawlib1.0          |
+-------------------------------------------+----------------+----------------------------------+

.. _gtasks_practice:

Good Task Creation Practice
====================================

.. _gtasks_practice_titles:

Titles
-------------------------------------

This should describe the bug/feature succinctly in 3-10 words, and generally
include the name of the code sector. A title needs to stand alone in describing
the task, bug, or feature request.

Avoid vague titles like "iochannel display bug" or "program not working". Also
avoid overly detailed titles like "flexarray has a segfault when more than
25,000 integers are stored in it".

Here are examples of some *good* titles:

* Allow Override of `ioc` in Goldlocks.

* OneString Code Refactoring: Improving to beat std::string

* [IOChannel] Rework how formatting flags are used and stored

* Blueshell: Cursor Navigation

* Strings not properly persisted in Punchline

.. _gtasks_practice_description:

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

* An explaination of why the feature is needed. This is important to
  prioritization. "It would be nice if..." takes a backseat to "This common
  scenario doesn't work without...".

* Any initial ideas you have on implementation (if applicable).

.. _gtasks_practice_tagging:

Tagging
--------------------------------
Slowvote
Tagging is very important for project management on Phabricator.

* Each task should have at least one **Project** tag. We recommend
  using more specific tags: subproject or subproject milestone, instead of
  master project, if available. (i.e. ``IOChannel [PawLIB]`` instead of
  ``PawLIB [Project]``).

* Each task MUST have at least one **Department** tag. This is usually
  either Programming, Content Development, UI Design, or Graphic Design.

* If this is a programming task, you should include a **Team** tag for the
  team whose code territory the task is in.
  (See :ref:`p_workflow_planning_territory`).

.. _gtasks_practice_status:

Status
-----------------------------------

A task's status should always be kept up-to-date by the task's assignee.

Tasks can have one of many statuses:

* Open: The task is ready to be worked on, possibly barring blocking tasks.

* Use The Force: Same as "Open", but the task is likely to be difficult.

* Proposed: We don't know whether the task will actually be worked on.

* On Hold: We stopped working on the task, and don't know whether we will
  resume work.

* Pending Help: All work has stopped on the task, pending help from someone
  else other than the assignee.

* Pending Review: The task is technically done, but outside review is
  required before it can be marked as completed.

* Resolved: The bug has been fixed.

* Completed: The feature/improvement/design/documentation task is finished.

* Rejected: We decided not to work on this task.

* Invalid: The task wasn't real. One example is filing a bug report, and then
  discovering the "bug" was actually a user error.

* Slain: We finished this difficult task, and deserve some applause!

.. _gtasks_creating:

Creating Tasks
==============================

.. _gtasks_creating_report:

Creating a Bug Report/Feature Request
-------------------------------------------

If you're reporting a bug or requesting a feature on a project you aren't
directly involved in developing, you should use the Bug Report or Feature
Request form, instead of the default New Task form.

You can find both of these forms on Maniphest, by clicking
:guilabel:`Create Task` in the upper-right corner, and then selecting
which form you want to use.

Fill out the following information...

* Title: Describe the bug or feature succinctly. Include the name of the
  code sector. (See :ref:`gtasks_practice_titles`).

* Proposed Urgency: This is how urgent you believe the task is. Actual
  prority and gravity will be determined by a project member later.

* Task Type: This is automatically set to Bug or Feature, depending on which
  form you're using.

* Description: Describe the bug or requested feature in detail.
  (See :ref:`gtasks_practice_description`).

* Visible To: This should generally be left on the default visibility
  (Global, All Users), unless you're a staff member and you need to hide
  an internal task from outside contributors.

* Editable By: Changes are tracked, so you *can* leave this set to All Users.
  At minimum, all staff should be able to edit.

* Tags: You **must** include at least one project, one department, and (for
  programming tasks) the team that the project belongs to.
  (See :ref:`gtasks_practice_tagging`).

* Subscribers: If you know of a particular user or users who should be aware
  of the task, or who you want input from, include them in Subscribers.

* Gravity Points: If you see this box, you can leave it blank. It will be
  filled in when the task is triaged.

After filling out the form, click :guilabel:`Create New Task` to submit it.
It will live in the Triage queue until it can be processed.

.. _gtasks_creating_task:

Creating a Task
--------------------------------------------

If you're creating a task for a project you're a part of, use the Create Task
form.

You can find both of these forms on Maniphest, by clicking
:guilabel:`Create Task` in the upper-right corner, and then selecting
:guilabel:`Create Task` from the menu.

* Title: Describe the task succinctly. Include the name of the
  code sector. (See :ref:`gtasks_practice_titles`).

* Task Type: There are six types of task. You should select one.

  * Bug: An error or misbehavior that should be fixed.

  * Feature: A new behavior or capability that should be added.

  * Improvement: Anything that makes the existing behavior or capability
    function *better*.

  * Design: Tasks that don't actually involve making changes to the project,
    but are related to planning changes.

  * Documentation: Tasks related to writing the comments and documentation
    for the project, but not otherwise changing the project.

  * Other: Anything and everything else.

..  sidebar:: Where Is WONTFIX?

    The common bug status "WONTFIX" is actually counterintuitive. It tells
    outsiders "we know the bug exists, and don't plan to do anything about
    it," effectively damaging the development team's reputation by making
    them look lazy and complacent.

    We use the terms "Invalid" and "Rejected" in place of "WONTFIX", which
    separates a bug report rejection into two categories: the bug never
    existed (invalid) and the bug cannot be fixed (rejected).

* Status: A task can have one of several statuses. The default is "Open".
  (See :ref:`gtasks_practice_status`).

* Assigned To: A single user who should work on this task. If you don't
  know who to assign a task to, just leave this blank.

* Description: Describe the bug or requested feature in detail.
  (See :ref:`gtasks_practice_description`).

* Tags: You **must** include at least one project, one department, and (for
  programming tasks) the team that the project belongs to.
  (See :ref:`gtasks_practice_tagging`).

* Proposed Urgency: This is set to "Established" by default, unless it was
  set by the original bug report/feature request creator. It remains locked
  for reference purposes.

* Priority: How soon should the task be completed?
  (See `QTM: Priority <http://standards.mousepawgames.com/qtm.html#priority>`_)

* Gravity: How important is the task to the project's completion?
  (See `QTM: Gravity <http://standards.mousepawgames.com/qtm.html#gravity-importance>`_)

* Relativity: How certain are you that the task can be completed by the given
  completion date or in the given number of hours?
  (See `QTM: Relativity <http://standards.mousepawgames.com/qtm.html#relativity-black-hole-probability-uncertainty>`_)

* Target Completion Date: When should we aim to have the task completed by?

* Estimated Hours: How long do you estimate it will take to complete this task?
  You should favor use of Target Completion Date instead, when possible.

* Friction: How many resources are available to help one in completing this task?
  (See `QTM: Friction <http://standards.mousepawgames.com/qtm.html#friction-available-help>`_)

* Required Skills: What does one need to know and be able to do to accomplish
  this task?

* Subscribers: If you know of a particular user or users who should be aware
  of the task, or who you want input from, include them in Subscribers.

* Visible To: This should generally be left on the default visibility
  (Global, All Users), unless you're a staff member and you need to hide
  an internal task from outside contributors.

* Editable By: Changes are tracked, so you *can* leave this set to All Users.
  At minimum, all staff should be able to edit.

* Gravity Points: This should match the number selected under *Gravity*.

After filling out the form, click :guilabel:`Create New Task` to submit it.
