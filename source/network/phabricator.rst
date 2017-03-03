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
:code:`Hawksnest [MousePaw Media]` Github application.

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

MousePaw Media staff may log into Phabricator using their LDAP credentials
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
    External link: [[ http://www.mousepawgames.com | MousePaw Media Website]]

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

When you **Subscribe** to an object, you will recieve email notifications for
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

At MousePaw Media, we use six major types of project objects. To avoid
confusion, we will use these terms as defined below. From now on, we'll use the
term "project object" or "subproject object" will be used to denote the actual
object type, instead of the kind of project object.

* We use the term **Project** or Master Project to refer to a particular
  top-level "Project" designated with "[Project]" in the name. These are the
  giant bins for organizing what we're working on - :code:`PawLIB`,
  :code:`Redstring`, :code:`Knitpicker`, and :code:`Anari`, to name a few.

* For our purposes, a **Subproject** refers to a subset of features within a Project.

* A **Group** designates permissions. We have several...

  * :code:`Bots [Group]` are system bots, which can access everything.

  * :code:`Trusted [Group]` is made up of all users who have "trusted"
    level access. (See :ref:`phab_security`).

  * :code:`Trusted Contributors [Group]` are non-staff members who have earned
    additional authority and privileges.

  * :code:`Informal Interns [Group]` are outside contributors who are
    working as unofficial interns. They receive much of the same training
    as interns, but are strictly volunteers, and not legally considered staff.

  * :code:`Interns [Group]` are official staff interns. They have the
    full authority of a staff member, but are currently in training.

  * :code:`Management [Group]` are official MousePaw Media managers.

  * :code:`Senior Staff [Group]` are official staff, usually those who have
    graduated from the internship program.

  * :code:`Senior Advisors [Group]` are official, yet inactive, staff. They
    served as senior staff at some point, but have officially left the company.
    However, they retain all the access and privileges of senior staff.

  * :code:`Staff [Group]` is a master group denoting all official staff.

* A **Department**, designated with "[Dept]", is a particular department at
  MousePaw Media. Membership is staff-only, and controlled by management.

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

* A **Label** is a project that is used exclusively for topical tagging and
  organization. We maintain labels for most of the technologies we use.
  (See :ref:`phab_projects_labels`).

See :ref:`gtasks_taskorproject`.

.. _phab_projects_membership:

Project Membership and Permissions
------------------------------------------

We use project objects to control user permissions.

Joining a Project
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

With most project object types, membership is controlled by management or
admins. However, with Projects, Subprojects, and Labels, we allow anyone
to **Join** the project.

Joining a Project or Subproject means you are (or plan to be) an active
contributor to the project.

Joining a Label is a way of declaring your experience in that topic. (See
:ref:`phab_projects_labels`).

Watching a Project
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If you **Watch** a project, you will receive notifications about anything
relating to that project, without you actually joining the project. This is
useful if joining the project is impossible or undesirable, but you want the
latest updates about it.

For example, if you're interested in the PawLIB project, but don't plan to
contribute to it, you should Watch it instead of Joining it.

..  _phab_projects_labels:

Labels
------------------------------------------

Labels are unusual in that, unlike other projects, they exist solely for
organizing things by topic. This is especially helpful for Ponder Questions
(see :ref:`phab_ponder`) and Maniphest Tasks (see :ref:`phab_maniphest`).

Another chief aim of a Label is to allow one to announce their expertise
about a topic. If you feel you have some degree of experience with Ubuntu,
for example, you would want to join the ``Ubuntu [Label]`` project. In turn,
if you needed help with pugixml, you could look at the members of the
``pugixml [Label]`` project.

..  NOTE:: While it is not possible to join some Labels directly because they
    have subprojects (i.e. "Linux"), you can join them by joining one of their
    subprojects (i.e. "Ubuntu", "Debian", or "Fedora").

Adding Labels
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Any Trusted user can create a Label, so if you don't see one you need, just
add it! The fastest way to do this is to click :guilabel:`Create Project` in the
upper-right corner of the Projects app, and select :guilabel:`Create Label`.

..  WARNING:: Labels should cover BROAD subject areas. Don't create labels
    for things like "Compiling C on Mac" or "Ubuntu 16.04".

..  _phab_projects_workboard:

Workboards
------------------------------------------

A Workboard is an organization tool which appears on each Project, Subproject,
and Milestone. Using the Master Project or Subproject's workboard is usually
best, as columns for each Milestone are automatically generated. You can
drag-and-drop tasks to move them between milestones.

..  NOTE:: A task will NOT appear on the workboards for both its Project and
    Subproject. If it is tagged with a Subproject, it will appear on that
    workboard. Thus, it is prudent to ensure each Subproject has Milestones.

..  _phab_security:

Security Policies
=========================================

The visibility and editability of nearly every object on Phabricator can
be controlled. To make the most common configurations easy to use, we've
set up several "security levels".

..  _phab_security_spaces:

Spaces
------------------------------------------

The quickest way to set an object's permissions is to set its **Space**. We
provide four.

..  NOTE:: The ID numbers are out of order because of when we created the Space.
    It's unfortunate, but we can't change these without an inordinate amount
    of hassle and risk.

* **S1: Global** allows any logged in user to access the object by default.
  This should be used for:

  * Anything relating to our open source projects (Tasks, Differentials, etc).

  * Most Phriction documents.

  * Most Ponder Questions.

* **S4: Trusted** allows all trusted users, including staff, to access the
  object by default. This should be used for:

  * Anything beneficial to regular contributors, but not useful to others.

  * Non-confidential staff-related objects.

  * Most Calendar Events.

* **S2: Staff-Only** only allows access by MousePaw Media Staff. This should
  be used for anything confidential to the company.

* **S3: Management** only allows access by MousePaw Media management.

..  _phab_security_permissions:

Visible To and Editable To
-----------------------------------------

Object access permissions can be further controlled by setting the
:guilabel:`Visible To` and :guilabel:`Editable By` fields.

While you can set this to just about anything, you should almost always set
this to "All Users," or use group-based permissions.

The most common projects to use for controlling these permissions are:

* **Trusted [Group]**: Identical to the Trusted Space.

* **Staff [Group]**: Identical to the Staff-only Space.

* **Management [Group]**: Identical to the Management Space.

Sometimes, we use a special group for certain objects:

* **Hiring [Group]**: All staff involved with hiring.

* **Standards Board [Access]**: All users who oversee our standards.

* **Repository Masters [Access]**: All users with control over the repositories.

.. _phab_maniphest:

Maniphest
=========================================

**Maniphest** is our bug tracker and task management system.

See also, :ref:`phab_projects`.

By default, Maniphest shows you the tasks assigned to you, grouped by their
Priority. You can search for other tasks by selecting a Query at left, or
by clicking :guilabel:`Edit Query` in the upper-right corner and entering
search criteria.

.. _phab_maniphest_create:

Creating Tasks
-----------------------------------------

You can create a new Maniphest Task by clicking :guilabel:`Create Task` in
the upper-right corner, and selecting the task type.

* :guilabel:`Create Task` allows you to create a new task, usually for a
  project *you* are working on.

* :guilabel:`Bug Report` creates a new Bug Report for a project you aren't
  working on. It only prompts you for the essential information, and leaves the
  actual prioritization to the person working on that bug.

* :guilabel:`Feature Request` is like Bug Report, but is focused on a requested
  feature instead of an actual bug.

* :guilabel:`Private Task` is for any task that should be hidden from everyone
  but you. Please only use this if the task is MousePaw Media-related, but
  unrelated to an actual project. For example, you may create an "Organize
  Work Inbox" task, or a "Read 'Dreaming in Code'" task - those sorts of tasks
  only matter to you, and might even overlap a similar task for another user
  if made public.

..  WARNING:: DO NOT use Private Task for *any* project-related task!

More detail about creating tasks, bug reports, and feature requests can
be found at :ref:`gtasks`.

.. _phab_maniphest_edit:

Managing Tasks
----------------------------------

Within an existing task, you will notice several actions you can take.

To the right of the description, you will see the following (depending on
permissions):

* :guilabel:`Edit Task` allows you to edit any field in the task. Use this
  ability courteously! Most QTM measures are intended to be set by a
  member of the project the task is related to.

* :guilabel:`Edit Related Tasks...` lets you connect this task to others.

  * :guilabel:`Create Subtask` creates a new task as a subtask of this one.

  * :guilabel:`Edit Parent Tasks` lets you select the tasks that this task
    is a "subtask" of; in other words, what tasks are BLOCKED by this one?

  * :guilabel:`Edit Subtasks` lets you select the tasks that are BLOCKING
    this one.

  * :guilabel:`Merge Duplicates In` marks other tasks as duplicates of this
    one. Instead of deleting the duplicate task, it simply links the two
    together for reference.

  * :guilabel:`Close As Duplicate` marks this task as a duplicate of another.

* :guilabel:`Edit Related Objects...` lets you connect other non-task objects
  to this one.

  * :guilabel:`Edit Commits` lets you select the repository commits that are
    related to this task. Connecting tasks and commits allows one to see the
    all the code changes associated with a feature or bugfix, which is very
    important for reference and historical reasons. A single task may have
    many commits associated with it.

  * :guilabel:`Edit Mocks` lets you select the Pholio Mocks related to this
    task. This is especially helpful for UI Design and Graphic Design tasks.

  * :guilabel:`Edit Revisions` lets you select the Differentials related to
    this task. Linking Tasks and Differentials is just as important as
    linking Tasks and Commits.

You'll also see the usual options relating to Subscribing, Tokens, and Flags.

If you scroll down to the comment box, you'll see an :guilabel:`Add Action...`
menu, which allows you to take additional actions on the task (depending
on your permissions.)

* :guilabel:`Change Status` sets the task's status. For a list of what each
  status means, see :ref:`gtasks_practice_status`.

* :guilabel:`Assign/Claim` allows you to assign the task to yourself,
  another person, or to place it "up for grabs" by assigning it to no one.

* :guilabel:`Change Priority` lets you set the task's priority, which is the
  only QTM measure that is likely to change once set. Out of courtesy, be sure
  NOT to set priority if the task belongs to a project you aren't a member of.

* In general, just forget that :guilabel:`Change Gravity Points` exists.
  Because of how we use this system, Gravity and Gravity Points should always
  be kept in sync.

* :guilabel:`Move on Workboard` lets you quickly change which column this
  task is in on the project workboard. (See :ref:`phab_projects_workboard`).

* :guilabel:`Change Project Tags` allows you to quickly change the project
  tags on the task. Remember to include the Department, Team, and Project.
  (See :ref:`gtasks_practice_tagging`).

* :guilabel:`Change Subscribers` lets you subscribe (or unsubscribe) users
  from a task. Remember to be courteous when using this! The most common
  reason to use this is if you want to notify a particular person about a task.

.. _phab_calendar:

Calendar
==================================

..  NOTE:: The Calendar tool is only accessible to staff.

Meetings, events, vacations, and deadlines are posted on the
`Calendar <https://phabricator.mousepawmedia.net/calendar>`_ application.
Events you are invited to are shown in green.

Events will automatically send email reminders 15 minutes before.

.. _phab_calendar_rsvp:

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

.. _phab_calendar_create:

Creating an Event
----------------------------------

You can create a new event by clicking on :guilabel:`Create Event` and selecting
an event type. There are three types:

* :guilabel:`Create Event` is for your run-of-the-mill event, especially
  the in-person variety.

* :guilabel:`Create Event [Reserve Jitsi]` is for any meeting which will use
  our Jitsi meeting rooms. This allows us to prevent schedule conflicts in the
  meeting room.

* :guilabel:`Create Hiring Event` is only for Hiring-related events. If you
  aren't a pare of the Hiring team, this will be hidden from you.

Then, fill out the form.

* The event Name should describe the event briefly. "Programming Meeting",
  "Anari Planning", or "1-on-1: Sergio/Andrew" are a few examples.

* Ensure your start and end days and times are correct. We typically check the
  "All Day Event" for deadline and vacation events.

* Select Invitees - the people you want to have attend the event.

* Write up a description. This is a good place for meeting agendas, or for
  event location and details.

* Consider changing the Icon. We typically use "Meeting" for large meetings,
  "Coffee Meeting" for small meetings (especially 1-on-1), "Official Business"
  for out-of-office business events, and "Holiday" for vacations and breaks.

* The default settings for Visible To and Editable By are usually fine, unless
  you need to hide an from anyone not in a particular group. For example, we
  usually set the visibility for hiring-related events to "Hiring [Dept]".

* You should *always* tag your event with the relevant Department at minimum.
  You may also want to include the appropriate Group, Team, or Project tags.
  Also, if you're using the Jitsi meeting room, make sure you include the
  ``Jitsi Room Reservation [Control]`` tag.

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

.. _phab_calendar_reservations:

Jitsi Room Reservations
-----------------------------------

The ``Jitsi Room Reservation [Control]`` tag page
`here <https://phabricator.mousepawmedia.net/project/view/116/>`_ provides
shortcuts to view and create reservations for the Jitsi meeting rooms, as
well as to join the room itself. Simply select an action from the menu to the
left of that page.

* :guilabel:`Join Room` opens the Jitsi room in the current tab.

* :guilabel:`View Room Reservations` shows all the upcoming room reservations.

* :guilabel:`Reserve Room` creates a new Event reserving the room.

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

To ask a new question, click :guilabel:`Ask Question` in the upper-right corner, and fill
out the form.

* The Question name should describe the problem in 3-15 words. The title is vital to others
  finding the question. Avoid being too vague ("Error with PawLIB"), but don't ask the whole
  question in the title either ("If I'm outputting a pointer memory dump, how do I format it
  with spaces between every fourth byte?") A good title would be something like "Custom Spacing
  on IOChannel Pointer Memory Dump".

* Question Details is where you go into detail. Describe the exact nature of your problem.
  If there is code involved, you should include an :abbr:`MCVE (Minimum, Complete, and
  Verfiable Example)` (taking cue from `StackOverflow <https://stackoverflow.com/help/mcve>`_).

* Answer Summary is only needed once the problem is solved. You may fill this in now if you're
  sharing knowledge in a Q&A format (which is great to do!), or wait and fill it in later to
  summarize the solution if multiple answers contributed.

* Visible To should almost always be set to Global and All Users.

* Include Tags for the department (i.e. Programming) and project(s) involved. We also have
  special labels for different technologies we use.

Finally, click :guilabel:`Submit` to post your question.

Managing a Question
----------------------------------

After you post a question, you'll want to watch it for comments and answers. Once the question
has been answered, you will want to mark the question as Closed by clicking
:guilabel:`Close Question`.

If there have been multiple helpful answers, you may also choose to add an Answer Summary by
clicking :guilabel:`Edit Question` and editing that field. This is helpful for ensuring
the exact solution to the problem is evident.

If you want to reopen a closed question to request more answers, click :guilabel:`Reopen Question`.

Commenting vs. Answering
-----------------------------------

If you need to ask for more information, or otherwise want to discuss the question or an answer
that has been posted, leave a comment. Answers should only be used to share a possible solution.

To add a comment, click :guilabel:`Add a Comment` below the question, or below an answer.

Answer a Question
----------------------------------

If you think you can answer a question, scroll to the bottom of the page and fill in the
:guilabel:`Answer` field. A few things to keep in mind:

* Be polite. Even if the answer is obvious, be respectful and professional.

* Posting links is fine, but you should also summarize the important stuff directly in your answer.

* Posting code is fine, but you should never post *only* code. Explain your solution: why and
  how does your code solve the problem? In programming questions, your goal should be to help
  the asker write the code him/herself.

.. _phab_differential:

Differential
==================================

.. _phab_audit:

Audit
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
