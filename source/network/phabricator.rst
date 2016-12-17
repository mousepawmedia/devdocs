Phabricator
#################################

Registration and Login
=================================

.. _phab_register_github:

Registration with Github
---------------------------------------

Non-staff may use Phabricator by registering with their Github account.
Requiring a Github account helps ensure that the large majority of accounts
are legitimate, and keep spam to a minimum.

To create a new Phabricator account with Github, click on the
:guilabel:`Login or Register Github` button. Then, authorize the
:code:`Hawksnest [MousePaw Games]` Github application.

.. _phab_login_github:

Login with Github
---------------------------------------

After you've registered with your Github account, you may log into Phabricator
anytime by clicking the :guilabel:`Login or Register Github` button.

..  IMPORTANT:: We do not store any login details for you - validation and
    authorization takes place entirely via Github. You are fully responsible
    for maintaining proper Github account security - we have no role in that.

.. _phab_login_ldap:

Login with LDAP
---------------------------------

MousePaw Games staff may log into Phabricator using their LDAP credentials
(username and password).

If you're a staff member, and you are having trouble logging in with your
LDAP credentials, contact IT to have your password reset.

..  IMPORTANT:: We do not issue LDAP credentials to non-staff under
    any circumstances.

General Structure
=================================

.. _phab_layout:

Basic Layout
---------------------------------

Phabricator offers numerous **Applications**, each of which can create and
manage a different kind of **object**. Nearly all objects are assigned a
unique ID, such as "D123" (Differential 123) or "T50"
(*Maniphest* Task 50).

This structure makes URLs very easy to guess in Phabricator. The page for the
Maniphest application would be :code:`https://phabricator.mousepawmedia.net/maniphest`.
Meanwhile, the page for task 50 (T50) would be :code:`https://phabricator.mousepawmedia.net/T50`.

.. _phab_comments:

Comments and Actions
---------------------------------

You can comment on just about every object in Phabricator. Scroll to the bottom
of any object's page to find the comment box and action menu.

Depending on the object and your permissions on it, different actions may be
available to you. You may select an action from the drop down box above the
comment box. The action (or actions) will be performed when you click the
:guilabel:`Submit` button.

.. _phab_remarkup:

Remarkup
---------------------------------

All comment boxes, as well as description fields and the wiki, using
**Remarkup**. Below is a quick reference::

    **Bold**
    //Italics//
    __Underline__
    !!Highlighted!!
    `Monospaces`
    ~~Strikethrough~~
    > Quoted text.

    * Bullets
    ** Nested bullets
    *** VERY nested bullets
    - Also a bullet
    -- You get the idea

    # Numbered list item.
    # Another numbered list item.
    # And so forth...

    Mention user: @username
    Mention project: #projectname
    Link to object: D123
    Embed object: {D123}
    Phriction link: [[gettingstarted]]
    Internal link: [[ /maniphest | Make a task. ]]
    External link: [[ http://www.mousepawgames.com | MousePaw Games Website]]

    ```
    This is a block of code.
    It is monospaced.
    It starts and ends with three backticks.
    ```

    ```
    name=Example, lang=python, lines=20
    This is a block of code using Python syntax highlighting.
    It has a title "Example".
    If it gets larger than 20 lines, a scrollbar will appear.
    ```

    NOTE: This is a note block, which renders blue.

    IMPORTANT: This is an important note block, which renders red.

    WARNING: This is a warning note block, which renders yellow.

    (NOTE) This is a note block, without the preceding text "NOTE:".

There are a lot more features in Remarkup, so you should take a few minutes
and review the official
`Remarkup Reference <https://secure.phabricator.com/book/phabricator/article/remarkup/>`_.

.. _phab_remarkup_memes:

Memes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

We have a number of memes, known as **Macros**, built in. Most of them are
just plain images, so you can add your own text. To do this, tap the meme
button (the smirking face on the comment box toolbar) and fill out the form.

..  NOTE:: I use a special system for organizing our Macros. All macro names
    start and end with a single underscore (:code:`_`). Macros that already
    have text on them start with an underscore and a colon (:code:`_:`).

If you want to browse through all of our available Macros, see the
`Macro <https://phabricator.mousepawmedia.net/macro>`_ application.

.. _phab_subscribing:

Subscribing
---------------------------------

When you **Subscribe** to an object, you will receieve email notifications for
any updates to that object. You are automatically subscribed to any object
you create.

To subscribe or unsubscribe to an object, click the :guilabel:`Subscribe` or
:guilabel:`Unsubscribe` button on the object page. It's usually near the top
right.

Be aware, others can also subscribe you to notifications. You should only
subscribe someone to an object if you require their attention or input on it.

.. _phab_flags:

Flags
---------------------------------

If you want to keep track of a particular object, you may want to **Flag** it.
Flags are like personal bookmarks on Phabricator. When you flag an object, you
can select a flag color, and even include a note to yourself.

To flag an object, click the :guilabel:`Flag For Later` button on the object
page. It's usually near the top right.

The `Flags <https://phabricator.mousepawmedia.net/flag>`_ application stores
all of your flags, so you can browse and manage them later.

.. _phab_tokens:

Tokens
---------------------------------

**Tokens** are a fun way of giving quick feedback on an object. Click on
:guilabel:`Award Token` to award one of the dozen or so available tokens
to the object.

You can also see the token rankings on the
`Tokens <https://phabricator.mousepawmedia.net/token>`_ application.

.. _phab_projects:

Projects
=========================================

A **Project** object is an arbitrary container that you can use to organize
other objects. A single project typically has a description and, in many cases,
a Kanban-style "Workboard". It may also include one or more **Subprojects**.

You can browse and manage projects from the
`Projects <https://phabricator.mousepawmedia.net/project>`_ application.

.. _phab_projects_types:

Types of Projects
------------------------------------------

At MousePaw Games, we use six major types of project objects. To avoid
confusion, we will use these terms as defined below. From now on, we'll use the
term "project object" or "subproject object" will be used to denote the actual
object type, instead of the kind of project object.

* We use the term **Project** or Master Project to refer to a particular
  top-level "Project" designated with "[Project]" in the name. These are the
  giant bins for organizing what we're working on - :code:`PawLIB`,
  :code:`Redstring`, :code:`Knitpicker`, and :code:`Anari`, to name a few.

* For our purposes, a **Subproject** refers to a

* A **Group** designates permissions. We have several...

  * :code:`Bots [Group]` are system bots, which can access everything.

  * :code:`Trusted Contributors [Group]` are non-staff members who have earned
    additional authority and privileges.

  * :code:`Interns [Group]` are official staff interns. They have the
    full authority of a staff member, but are currently in training.

  * :code:`Management [Group]` are official MousePaw Games managers.

  * :code:`Senior Staff [Group]` are official staff, usually those who have
    graduated from the internship program.

  * :code:`Senior Advisors [Group]` are official, yet inactive, staff. They
    served as senior staff at some point, but have officially left the company.
    However, they retain all the access and privileges of senior staff.

  * :code:`Staff [Group]` is a master group denoting all official staff.

* A **Department**, designated with "[Dept]", is a particular department at
  MousePaw Games. Membership is staff-only, and controlled by management.

* A **Team** is a group within a department. A team is generally characterized
  by a particular approach and skill set, and maintains a specific set of
  projects (their "code territory"). We have two teams:

  * The :code:`BSS [Team]`, or "Black Sheep Squadron", specializes in data
    storage and processing. Their "code territory" includes PawLIB,
    SIMPLEXpress, PuppeteerXML, Trailcrest, and Ratscript.

  * The :code:`IMF [Team]` specializes in multimedia and graphics. Their
    "code territory" includes Anari, Lightrift, and Stormsound.

  * The :code:`A-Team [Team]` specializes in integration, threading, and
    multiprocessing. The team is currently inactive.

* A **Control** is a special type of project that controls automation on
  objects tagged with it.

* **Access** designates a special permission group.

* A **Subproject** belongs to a Project for further organizing objects. For
  example, PawLIB has subprojects for OneString, Tripline, and Goldilocks,
  to name a few. A subproject should define a large, distinct collection of
  features with a unique name. These aren't arbitrary divisions. Use with
  caution!

* A **Milestone** is a special kind of subproject, which belongs to a Master
  Project. It is used to designate versions.

See :ref:`phab_maniphest_task_or_project`.

.. _phab_projects_membership:

Project Membership and Permissions
------------------------------------------

We use project objects to control user permissions.

Joining a Project
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

With most project object types, membership is controlled by management or
admins. However, with Projects and Subprojects, we allow anyone to **Join** the
project.

Watching a Project
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If you **Watch** a project, you will receive notifications about anything
relating to that project, without you actually joining the project. This is
useful if you cannot join a project, but want the latest updates about it.

Workboards
------------------------------------------

A Workboard is an organization tool which appears on each Project, Subproject,
and Milestone. Using the Master Project or Subproject's workboard is usually
best, as columns for each Milestone are automatically generated. You can
drag-and-drop tasks to move them between milestones.

..  NOTE:: A task will NOT appear on the workboards for both its Project and
    Subproject. If it is tagged with a Subproject, it will appear on that
    workboard. Thus, it is prudent to ensure each Subproject has Milestones.

.. _phab_maniphest:

Maniphest
=========================================

**Maniphest** is our bug tracker and task management system.

See also, :ref:`phab_projects`.

.. _phab_maniphest_task_or_project:

Task or Project
----------------------------------------

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

.. _phab_maniphest_practice:

Good Task Creation Practice
------------------------------------------

.. _phab_maniphest_practice_titles:

Titles
^^^^^^^^^^^^^^^^^

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

.. _phab_maniphest_practice_description:

Description
^^^^^^^^^^^^^^^^^^^

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

.. _phab_maniphest_practice_tagging:

Tagging
^^^^^^^^^^^^^^^^^^^^^^

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

.. _phab_maniphest_creatingreport:

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
  code sector. (See :ref:`phab_maniphest_practice_titles`).

* Proposed Urgency: This is how urgent you believe the task is. Actual
  prority and gravity will be determined by a project member later.

* Task Type: This is automatically set to Bug or Feature, depending on which
  form you're using.

* Description: Describe the bug or requested feature in detail.
  (See :ref:`phab_maniphest_practice_description`).

* Visible To: This should generally be left on the default visibility
  (Global, All Users), unless you're a staff member and you need to hide
  an internal task from outside contributors.

* Editable By: Changes are tracked, so you *can* leave this set to All Users.
  At minimum, all staff should be able to edit.

* Tags: You **must** include at least one project, one department, and (for
  programming tasks) the team that the project belongs to.
  (See :ref:`phab_maniphest_practice_tagging`).

* Subscribers: If you know of a particular user or users who should be aware
  of the task, or who you want input from, include them in Subscribers.

* Gravity Points: If you see this box, you can leave it blank. It will be
  filled in when the task is triaged.

After filling out the form, click :guilabel:`Create New Task` to submit it.
It will live in the Triage queue until it can be processed.

.. _phab_maniphest_creatingtask:

Creating a Task
-----------------------------------

If you're creating a task for a project you're a part of, use the Create Task
form.

You can find both of these forms on Maniphest, by clicking
:guilabel:`Create Task` in the upper-right corner, and then selecting
:guilabel:`Create Task` from the menu.

* Title: Describe the task succinctly. Include the name of the
  code sector. (See :ref:`phab_maniphest_practice_titles`).

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

* Assigned To: A single user who should work on this task. If you don't
  know who to assign a task to, just leave this blank.

* Description: Describe the bug or requested feature in detail.
  (See :ref:`phab_maniphest_practice_description`).

* Tags: You **must** include at least one project, one department, and (for
  programming tasks) the team that the project belongs to.
  (See :ref:`phab_maniphest_practice_tagging`).

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

.. _phab_calendar:

Calendar
==================================

..  NOTE:: The Calendar tool is only accessible to staff.

Meetings, events, vacations, and deadlines are posted on the
`Calendar <https://phabricator.mousepawmedia.net/calendar>`_ application.
Events you are invited to are shown in green.

Events will automatically send email reminders 15 minutes before.

Responding to an Event
----------------------------------

You should **always** respond to any event you are invited to, whether
accepting or declining, as soon as you know. If you are invited to an
event, you may accept by clicking :guilabel:`Accept` or :guilabel:`Join Event`.
You may decline by clicking :guilabel:`Decline` or :guilabel:`Decline Event`.

If you decline, you should consider commenting with the reason.

After accepting an event, you can set your **Availability** by using the
:guilabel:`Availability` control in the upper right corner of the event page.
This is the status icon that will appear next to your username around
Phabricator. It's useful for letting people know when you are and aren't
available.

Creating an Event
----------------------------------

You can create a new event by clicking on a day and a timeslot, or by clicking
:guilabel:`Create Event`. Then, fill out the form.

* The event Name should describe the event briefly. "Programming Meeting",
  "Anari Planning", or "1-on-1: Sergio/Andrew" are a few examples.

* Ensure your start and end days and times are correct. We typically check the
  "All Day Event" for deadline and vacation events.

* Select Invitees - the people you want to have attend the event.

* Write up a description. This is a good place for meeting agendas, or for
  event location and details.

* Consider changing the Icon. We typically use "Meeting" for large eetings,
  "Coffee Meeting" for small meetings (especially 1-on-1), "Official Business"
  for out-of-office business events, and "Holiday" for vacations and breaks.

* The default settings for Visible To and Editable By are usually fine, unless
  you need to hide an from anyone not in a particular group. For example, we
  usually set the visibility for hiring-related events to "Hiring [Dept]".

* You should *always* tag your event with the relevant Department at minimum.
  You may also want to include the appropriate Group, Team, or Project tags.

* As a habit, you should include the same people in Subscribers as you set in
  Invitees, so they get email notifications about attendance.

Click :guilabel:`Create Event` to create your event.

After creating an event, you can edit it to change the Host - the person who
is organizing the event. This is useful if you're creating the event on behalf
of another user.

Recurring Events
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

You can make an event repeat itself automatically. To do this, you must create
the first event. Then, on the event's page, click :guilabel:`Make Recurring`.

On the popup window, set the Frequency and, if desires, the Repeat Until date.
Then click :guilabel:`Save Changes`.

Later, you can click :guilabel:`Edit Recurrence` to change these settings.

.. _phab_phriction:

Phriction
==================================

.. _phab_phriction_vs_docs:

Phriction vs. Docs
----------------------------------

It can be hard to know what belongs in documentation, and what belongs in
Phriction. Here's the basic principle:

* Anything that the end-user needs to see goes in documentation. This generally
  includes usage instructions.

* Everything else belongs on Phriction. This includes internal (developer)
  docs, specs, planning notes, and design work.

.. _phab_ponder:

Ponder
==================================

**Ponder** is our own personal "StackOverflow". It's a great place to ask
questions and share knowledge regarding our libraries, as well as usage of
our development tools.

Ask a Question
----------------------------------

Answer a Question
----------------------------------

.. _phab_audit:

Audit
==================================

.. _phab_differential:

Differential
==================================

.. _phab_diffusion:

Diffusion
==================================

.. _phab_pholio:

Pholio
==================================

.. _phab_slowvote:

Slowvote
==================================

.. _phab_paste:

Paste
==================================

.. _phab_phurl:

Phurl
==================================
