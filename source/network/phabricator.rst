..  _phab:

Phabricator
#################################

Registration and Login
=================================

MousePaw Media staff and interns should log in with LDAP. Everyone else logs
in using GitHub.

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

..  important:: We do not store any login details for you - validation and
    authorization takes place entirely via Github. You are fully responsible
    for maintaining proper Github account security - we have no role in that.

.. _phab_login_ldap:

Login with LDAP
---------------------------------

MousePaw Media staff may log into Phabricator using their LDAP credentials
(username and password).

If you're a staff member, and you are having trouble logging in with your
LDAP credentials, contact IT to have your password reset.

General Structure
=================================

.. _phab_layout:

Basic Layout
---------------------------------

Phabricator offers numerous **Applications**, each of which can create and
manage a different kind of **object**. Nearly all objects are assigned a
unique ID, such as "D123" (Differential Revision 123) or "T50"
(*Maniphest* Task 50).

This structure makes URLs very easy to guess in Phabricator. The page for the
Maniphest application would be :code:`https://phab.mousepawmedia.com/maniphest`.
Meanwhile, the page for task 50 (T50) would be :code:`https://phab.mousepawmedia.com/T50`.

However, if you aren't sure of the object code, just start typing in
Phabricator's universal search box in the upper-right. Suggestions will
appear as you type, and you can click the one you want.

.. _phab_toolbar:

Toolbar
-------------------------

The toolbar is always visible at the top of Phabricator. From left to right...

* You can click the **Phabricator icon** to return to the Right Now page.
  (See :ref:`phab_rightnow`).

* The **Notifications menu** (the bell) lists all notifications. Click one to
  open the object it discusses and mark it as read, or click
  :guilabel:`Mark All Read` to mark them all as read.

* The **Favorites menu** (the bookmark icon) provides shortcuts for many common tasks.
  You can click :guilabel:`Edit Favorites` to add more items to this menu.

* The **User menu** (your profile picture) provides shortcuts to your profile,
  settings, and logout.

* The **Search bar** lets you search for any application or object by name
  or object ID. The menu on the left side of the search bar provides more
  search options, including Advanced Search.

.. _phab_rightnow:

Right Now
--------------------------

When you first log onto Phabricator, you'll start on the Right Now page.
This will show the latest activity and help you narrow in on what you need
to do.

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

All comment boxes, as well as description fields and the wiki, use
**Remarkup**. Below is a quick reference::

    **Bold**
    //Italics//
    __Underline__
    !!Highlighted!!
    `Monospaced`
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
    External link: [[ https://www.mousepawmedia.com | MousePaw Media Website]]

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

..  note:: I use a special system for organizing our Macros. All macro names
    start and end with a single underscore (:code:`_`). Macros that already
    have text on them start with an underscore and a colon (:code:`_:`).

If you want to browse through all of our available Macros, see the
`Macro <https://phab.mousepawmedia.com/macro>`_ application.

.. _phab_subscribing:

Subscribing
---------------------------------

When you **Subscribe** to an object, you will receive email notifications for
any updates to that object. You are automatically subscribed to any object
you create.

To subscribe or unsubscribe to an object, click the :guilabel:`Subscribe` or
:guilabel:`Unsubscribe` button on the object page. It's usually near the top
right.

Be aware, others can also subscribe you to notifications. You should only
subscribe someone to an object if you require their attention or input on it.

If you want to stay subscribed to an object, but don't want Notifications,
you can click :guilabel:`Mute Notifications`. (This is helpful, for example,
if you created the object, but don't want to keep getting emails about it.)

.. _phab_flags:

Flags
---------------------------------

If you want to keep track of a particular object, you may want to **Flag** it.
Flags are like personal bookmarks on Phabricator. When you flag an object, you
can select a flag color, and even include a note to yourself.

To flag an object, click the :guilabel:`Flag For Later` button on the object
page. It's usually near the top right.

The `Flags <https://phab.mousepawmedia.com/flag>`_ application stores
all of your flags, so you can browse and manage them later.

.. _phab_tokens:

Tokens
---------------------------------

**Tokens** are a fun way of giving quick feedback on an object. Click on
:guilabel:`Award Token` to award one of the dozen or so available tokens
to the object.

You can also see the token rankings on the
`Tokens <https://phab.mousepawmedia.com/token>`_ application.

.. _phab_projects:

Projects
=========================================

A **Project** object is an arbitrary container that you can use to organize
other objects. A single project typically has a description and, in many cases,
a Kanban-style "Workboard". It may also include one or more **Subprojects**.

You can browse and manage projects from the
`Projects <https://phab.mousepawmedia.com/project>`_ application.

.. _phab_projects_types:

Types of Projects
------------------------------------------

At MousePaw Media, we use six major types of project objects. To avoid
confusion, we will use these terms as defined below. From now on, we'll use the
term "project object" or "subproject object" to denote the actual
object type, instead of the kind of project object.

* We use the term **Project** to refer to a particular product we're producing.
  These are the bins for organizing what we're working on - :code:`Goldilocks`,
  :code:`Ratscript`, :code:`Stormsound`, and :code:`Anari`, to name a few.

* A **Label** exists purely for organizing other things by technologies or
  concepts. See :ref:`phab_projects_labels`.

* A **Group** designates permissions. The hierarchy is as follows:

  * :code:`Trusted [Group]` is made up of all users who have "trusted"
    level access. (See :ref:`phab_security`).

    * :code:`Bots [Group]` are accounts for automated systems, with full
      access to everything.

    * :code:`Trusted Contributors [Group]` are non-staff members who have earned
      additional authority and privileges.

    * :code:`Alumni [Group]` are internship graduates who have left the company.
    They retain Trusted-level access and privileges.

    * :code:`Staff [Group]` is all current staff...

      * :code:`Interns [Group]` are current MousePaw Media interns. This is
        further subdivided into :code:`Provisional Intern [Group]` and
        :code:`Intern [Group]`.

      * :code:`Regular Staff [Group]` are non-intern staff.

        * :code:`Mentors [Group]` are internship mentors.

        * :code:`Journeyman [Group]` are internship graduates who haven't yet
          reached Senior rank.

        * :code:`Senior [Group]` are Senior-level staff.

    * :code:`Management [Group]` are official MousePaw Media managers.

* A **Department**, designated with "[Dept]", is a particular department at
  MousePaw Media. Membership is staff-only, and controlled by management.

* A **Control** is a special type of project that controls automation on
  objects tagged with it.

* **Access** designates a special permission group.

* A **Label** is a project that is used exclusively for topical tagging and
  organization. We maintain labels for most of the technologies we use.
  (See :ref:`phab_projects_labels`).

See :ref:`gtaskcreate_taskorproject`.

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

..  note:: While it is not possible to join some Labels directly because they
    have subprojects (i.e. "Linux"), you can join them by joining one of their
    subprojects (i.e. "Ubuntu", "Debian", or "Fedora").

Adding Labels
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Any Trusted user can create a Label, so if you don't see one you need, just
add it! The fastest way to do this is to click :guilabel:`Create Project` in the
upper-right corner of the Projects app, and select :guilabel:`Create Label`.

..  warning:: Labels should cover BROAD subject areas. Don't create labels
    for things like "Compiling C on Mac" or "Ubuntu 16.04".

..  _phab_projects_workboard:

Workboards
------------------------------------------

A Workboard is an organization tool which appears on each Project.
We use Workboards for tracking the tasks we've selected for our current
sprint. Be careful not to rearrange these on your own. Selecting and priorizing
tasks in a sprint is a team activity.

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

..  note:: The ID numbers are out of order because of when we created the Space.
    It's unfortunate, but we can't change these without an inordinate amount
    of hassle and risk.

* **S1: Global** allows any logged in user to access the object by default.
  This should be used for:

  * Anything relating to our open source projects (Tasks, Revisions, etc).

  * Most Phriction documents.

  * Most Ponder Questions.

* **S4: Trusted** allows all trusted users, including staff, to access the
  object by default. This should be used for:

  * Anything beneficial to regular contributors, but not useful to others.

  * Non-confidential staff-related objects.

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

.. _phab_phame:

Phame
==================================

**Phame** is our internal blogging tool, which we use for our Dev Journals
and our company newsletter.

.. _phab_phame_reading:

Reading on Phame
----------------------------------

From the main page of Phame, you can see a feed of all Posts, starting from
the most recent. To the right is the list of all Blogs.

To view a Post, click its title. At the bottom, you can leave comments.

To view a Blog, from the main page of Phame, click the Blog name at right.
Alternatively, on a Post, click the Blog name on the breadcrumb trail at the
top of the page. From the :guilabel:`Actions` menu, you can Subscribe or
Unsubscribe, Mute Notifications, and more.

.. _phab_phame_posting:

Posting on Phame
----------------------------------

There are two ways to start a new Post:

* From the main page of Phame, click the :guilabel:`+` icon next to the Blog
  you want to post to.

* Navigate to the Blog you want to post to, and select :guilabel:`Actions`
  and :guilabel:`Write Post`.

Give your post a Title, and optionally, a Subtitle. You can keep your post's
Visibility as :guilabel:`Published` to publish when you first save, or else
change it to :guilabel:`Draft`, so you can choose when to publish later.

The Body is the main content of the post. The template provided is for
Dev Journal posts, which you can use or modify as you need. You can use
the full Remarkup markdown language here, including formatting, embedded
objects, images, memes, and emojis. Make it yours!

Tags is purely optional, although it may be helpful to add Labels related
to your post. For example, if you learned something amazing about Clang,
you might consider adding "Clang/LLVM [Label]" here.

Normally, anyone who subscribes to your blog, and anyone you mention directly
in your post, will be notified of your post. However, if you want someone
else to know, add them to subscribers here. (Be advised, they may not get an
email about the initial post, only updates to it. It's a glitch.)

When you're done, click :guilabel:`Create New Post`. That will create your
Post and take you directly to it.

From here, you can further modify your post using the :guilabel:`Actions`
menu:

* :guilabel:`Publish` or :guilabel:`Unpublish` will change your post to
  Published or Draft status, respectively. If you started as a Draft, click
  :guilabel:`Publish` to make the post visible to all!

* :guilabel:`Edit Post` takes you back to the post editing screen.

* :guilabel:`Edit Header Image` lets you add a header image. These are NOT
  resized and cropped automatically. If you want to add one, I recommend
  the dimensions 960x320.

* :guilabel:`Move Post` moves the post to a different Blog.

* :guilabel:`View History` shows the whole edit history of the post.

* :guilabel:`Archive` effectively deletes the post, although you can
  Unarchive it later if you so choose.

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

More detail about creating tasks, bug reports, and feature requests can
be found at :ref:`gtaskcreate`.

.. _phab_maniphest_edit:

Managing Tasks
----------------------------------

Within an existing task, you will notice several actions you can take.

To the right of the description, you will see the following (depending on
permissions):

* :guilabel:`Edit Task` allows you to edit any field in the task. Use this
  ability courteously! Most fields are intended to be set by a
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

  * :guilabel:`Edit Revisions` lets you select the Revisions related to
    this task. Linking Tasks and Revisions is just as important as
    linking Tasks and Commits.

You'll also see the usual options relating to Subscribing, Tokens, and Flags.

If you scroll down to the comment box, you'll see an :guilabel:`Add Action...`
menu, which allows you to take additional actions on the task (depending
on your permissions.)

* :guilabel:`Change Status` sets the task's status. For a list of what each
  status means, see :ref:`gtaskcreate_practice_status`.

* :guilabel:`Assign/Claim` allows you to assign the task to yourself,
  another person, or to place it "up for grabs" by assigning it to no one.

* :guilabel:`Change Priority` lets you set the task's priority, which is the
  only QTM measure that is likely to change once set. Out of courtesy, be sure
  NOT to set priority if the task belongs to a project you aren't a member of.

* In general, just forget that :guilabel:`Change Energy Points` exists.
  Because of how we use this system, this should always be based on the
  Distance, Friction, and Relativity fields.

* :guilabel:`Move on Workboard` lets you quickly change which column this
  task is in on the project workboard. (See :ref:`phab_projects_workboard`).
  Be careful about this: we only move tasks on workboards as a group, as part
  of sprint planning.

* :guilabel:`Change Project Tags` allows you to quickly change the project
  tags on the task. Remember to include the Department and Project.
  (See :ref:`gtaskcreate_practice_tagging`).

* :guilabel:`Change Subscribers` lets you subscribe (or unsubscribe) users
  from a task. Remember to be courteous when using this! The most common
  reason to use this is if you want to notify a particular person about a task.

.. _phab_phriction:

Phriction
==================================

Phriction is our wiki. For the most part, anyone on our team can edit any
page. We use it to share ideas, collaborate on project specifications and
design notes, and keep all of our collective brilliance organized.

Pages can be nested under other pages. The URL and breadcrumb trail can always
tell you where you are in the wiki. For example, :code:`/w/anari/spec/`
is the :code:`spec` page, under the :code:`anari` page. In the URL, :code:`w`
is the root of the wiki.

.. _phab_phriction_reading:

Reading the Wiki
------------------------------------

At the very top is breadcrumb trail showing where you are in the wiki.

Next is the Title. There may also be a button with lines just to the left.
Clicking this shows you a list of all headers in the document. Click one to
jump to that header.

Below the Title is an indicator of when the last change was made.

The body of the wiki document is below that. Nothing more needs to be said
about that. Just read it!

Below the page is a bit of information about the last change, followed by
a :guilabel:`Document Hierarchy` listing all documents that are under this one.
Typically we'll try to keep links to these documents in the wiki page itself,
but this ensures nothing can get misplaced.

Next is the history of actions and comments on the document.
Click :guilabel:`(Show Details)` to see what changed in that edit.
Alternatively, you can click :guilabel:`View History` from the menu
to the right of the document itself to see the whole history of edits to
the wiki document.

At the very bottom, you can read and leave comments.

.. _phab_phriction_actions:

Wiki Actions
------------------------------------

:guilabel:`Edit Document` lets you edit the document. If you're able to even
click this button, you do NOT need permission to make edits! Just make any
addition or change you think should be made. Wikis are meant to be
freely collaborative.

:guilabel:`View History` shows every change ever made to the page. Any change
can be seen individually, and even reverted if necessary.

:guilabel:`Move Document` allows you to change the path (in the URL) to the
page.

:guilabel:`Delete Document` deletes the document. This too can be reverted.

:guilabel:`Printable Page` shows a printable version of the wiki page,
perfect for distraction free reading or for printing.

.. _phab_phriction_editing:

Editing a Wiki Page
------------------------------------

Editing a wiki page is straightforward. The document is in Remarkup, like
everything else in Phabricator.

Before publishing, briefly describe your changes in :guilabel:`Edit Notes`
at the bottom of the form.

:guilabel:`Tags`` is useful for tagging pages with relevant Projects and
Labels, to help find pages later.

The :guilabel:`Visible To` and :guilabel:`Editable By` fields should be
left as open as possible. Unless there's a very specific reason to
change the policy, most pages should be viewable by Public, and editable
by Trusted.

When you're ready to save your changes, you have two options:

* :guilabel:`Save and Publish` immediately posts your changes.

* :guilabel:`Save Draft` saves your changes as a draft. These are also
  viewable by anyone, but won't be shown by default when someone views the
  document. This is helpful if you want feedback on your changes before
  publishing officially.

If you save as draft, you can publish by clicking :guilabel:`Publish Draft`
while viewing the draft on the document itself.

..  note:: To change where a document lives instead, see the
    :guilabel:`Move Document` option from the menu on the document itself.

.. _phab_phriction_creating:

Creating a New Wiki Page
------------------------------------

There are two ways to create a document.

The first is to click :guilabel:`New Document` on the upper-right-most corner
of Phriction, and filling out the path you want. Every document except the
last one in the path must already exist.

The other way, which is usually easier, is to add a link to the new document
directly to the document you want to create the new document under. For example,
if I wanted to create the document :code:`whiteboard/cat_pictures`, I would
edit the :code:`whiteboard` document and add the link
:code:`[[ ./cat_pictures ]]`. After saving the document, you'll see the new
link is red, indicating that the linked document doesn't exist yet. Click
the link, and on the new page, click :guilabel:`Create this Document`.

..  _phab_phriction_links:

About Wiki Links
-----------------------------------

In Remarkup, you can link to any Phriction wiki document like this:
:code:`[[ path/to/doc ]]`. Notice that I do *not* start the path with a
leading forward slash, which would point to an actual URL on Phabricator
instead.

Within Phriction itself, it's tedious to have to type the whole path
for every wiki link you include in a document. Instead, you can use relative
paths. A dot (:code:`.`) indicates the current document, so
:code:`[[ ./thing ]]` would point to a document :code:`thing` under the current
document. A double dot indicates the parent document, so :code:`[[ ../other ]]`
would point to a document :code:`other` which is a *sibling* of the current
document. Similarly, :code:`[[ ../../whatever ]]` would go back two levels, and
then down to the page :code:`whatever`.

.. _phab_phriction_vs_docs:

Phriction vs. Docs
----------------------------------

It can be hard to know what belongs in documentation, and what belongs in
Phriction. Here's the basic principle:

* Anything that the end-user needs to see goes in documentation. This generally
  includes usage instructions.

* Everything else belongs on Phriction. This includes internal (developer)
  docs, specs, planning notes, and design work.

However, in some cases, a collaborative document Nextcloud may suit the purpose
of the document better. This is especially true if multiple people need to
edit the document at once, such as during sprint planning. Consider moving
the finished document to the wiki when you're done.

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
  Verifiable Example)` (taking cue from `StackOverflow <https://stackoverflow.com/help/mcve>`_).

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

Differential is for pre-commit code reviews - analogous to GitHub pull
requests. In general, changes to code has to pass pre-commit code review
before being accepted to the main repository.

A collection of changes for review is called a **Revision** - a single update to
a Revision is called a **Diff**.

..  note:: You'll often hear us referring to Revisions as "Diffs" in
    conversation, such as "Did you Diff the code?", "What's the Diff?" or
    "Did we land that Diff?" On occasion, we may also call a Revision a
    "Differential," referring to the app. Just remember - colloquially,
    a Revision, a Diff, and a Differential all refer to the same thing:
    a "Differential Revision".

.. _phab_differential_anatomy:

Browsing Revisions
---------------------------------

When you first open the Differential application on Phabricator, you'll be
on the :guilabel:`Active Revisions` query. This shows all the Revisions
that you are involved in.

On the left, you can use the other built-in queries, or else you can click
:guilabel:`Edit Query` at the upper-right.

Anatomy of a Revision
---------------------------------

A Revision contains a batch of changes to a repository's files. There is
a lot to one, so let's break it down.

.. _phab_differential_anatomy_details:

Details
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* **Title**: A simple description of the changes.

* **Reviewers**: The individuals who will be reviewing the code. There are
  two types: regular reviewers and blocking reviewers. The latter *must*
  approve the code before it can be landed.

* **Summary**: A detailed description of the goals of the Revision.

* **Test Plan**: How will a reviewer know the goals of the Revision are
  met? This is not an optional field!

* **Revert Plan**: This optional field describes how the changes can be
  undone once the Revision has been landed. This is usually only needed
  if Revision's changes involve some complicated tweaks to other existing
  code.

Note that there are two more fields at the bottom of the menubox to the right.

* **Tags**: The Projects that this Differential is associated with. This should
  include Department, Team, and Project, as well as any appropriate Labels.

* **Subscribers**: These users will be notified about changes. This field
  is actually *very important* - if the ``No Build [Control]`` or
  ``No Test [Control]`` tags are included here, Jenkins will NOT build
  the code or run tests (respectively).

.. _phab_differential_anatomy_diffdetails:

Diff Detail
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The Diff Detail box shows more information about the latest update to the
Revision.

* **Repository**: The repository that the Revision belongs to. Make sure
  this is correct, or weird things can happen.

* **Branch**: The branch we're working on. This should *never* be ``devel``,
  ``fresh``, or ``stable``.

* **Lint**: If we have linters (static code checkers) configured for the
  repository, this will mark whether the changes passed linting. This should
  always been green before landing.

* **Build Status**: The status of the automatic building (CI) system. Nearly
  every Revision will report whether Arcanist's lint and unit tests
  (although the latter is never configured). If there are any
  Harbormaster/Jenkins builds configured, their status will be listed here.
  (See :ref:`harbormasterjenkins`).

.. _phab_differential_anatomy_history:

History
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Below the details section is the history - all comments, updates, Jenkins
test results, and other actions are posted here. The most recent stuff is
visible by default, but older updates can be seen by clicking
:guilabel:`Show Older Changes` at the top of the history.

.. _phab_differential_anatomy_revisioncontents:

Revision Contents
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The **Revision Contents** box provides an overview of the changes in the
Revision. It has three tabs: Files, History, and Commits

The **Files** tab shows all of the files that were affected by this
Revision. It lists the change type (``A`` for added, ``M`` for modified,
``V`` for renamed/moved, and ``D`` for deleted), the filename, the number of
lines changed, and the Owners Package(s) the file belongs to.
(See :ref:`phab_owners`).

The **History** tab lists each update made to the Revision. A single
Revision is usually composed of multiple "Diffs", which are listed
here, along with their unique ID. The Base is the already-landed repository
commit that the Diff is based on. Next, we'll see the Description, the date
Created, and the Lint status (ignore the Unit status).

.. _phab_differential_anatomy_diff:

The Diff
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Most of the rest of the Revision screen is devoted to showing the changes
themselves.

..  note:: How Revisions are displayed depends on your Diff Preferences.
    To change these, click your Profile picture, select :guilabel:`Settings`,
    and :guilabel:`Diff Preferences`.

Each file is displayed separately, with changes highlighted in red (for
deletions) and green (for additions).

Comments may be left inline by clicking a line number. Full Remarkup
is available on inline comments. Click :guilabel:`Save Draft` when you're done.
Inline comments are not submitted until you click the :guilabel:`Submit`
button towards the bottom-right of the page.

The **File Tree** is visible on the left side of the screen. (If it isn't,
turn it on in Diff Preferences. Then, you can tap the :kbd:`f` key to toggle
the File Tree while viewing a Revision.)

.. _phab_differential_creating:

Creating a Revision
-------------------------------------

There are two ways to create a Revision.

.. _phab_differential_creating_arc:

Method 1: Arcanist
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

..  sidebar:: The Ten Commandments of Git

    #I-X: NEVER WORK ON THE DEVEL OR STABLE BRANCH!

The easiest and most common is to use **Arcanist** in your command line. (See
:ref:`gitarc`). Revisions are tied to a Git branch, so you simply create a
new branch for your work. Typically, after making some changes to the code, you
only need to run...

..  code-block:: bash

    $ git add .
    $ git commit
    $ arc diff

That will publish all the unpublished commits to a Revision - either
updating the currently open Diff for that branch, or else creating a new
one.

.. _phab_differential_creating_patch:

Method 2: Uploading a Patch
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If you don't have access to Arcanist, you can still upload your changes
to a Revision via a patch file.

You should still work on a separate branch from `` devel ``. Create a patch
via...

..  code-block:: bash

    $ git add .
    $ git commit
    $ git diff

On Revision, click :guilabel:`Create Diff`.

On that screen, either paste the contents of the patch you just created into
the box, or attach the patch file using :guilabel:`Choose File`. Be sure to
set the :guilabel:`Repository`. Finally, click :guilabel:`Create Diff`.

Edit the Revision you just created, and add the Title, Description,
Tags, Test Plan, Reviewers, and Subscribers.

Later, you can update this Revision using the :guilabel:`Update Diff`
button on the Revision's page.

.. _phab_differential_managing:

Managing a Revision
--------------------------------

There are a lot of actions you can take on a Revision, depending on
whether you're the author or a reviewer.

On the right-hand menu at the top of the page, we have the following:

* :guilabel:`Edit Revision` lets you edit the Revision Details.

* :guilabel:`Update Diff` allows you to upload a new version of the
  Revision using a patch file.

* :guilabel:`Download Raw Diff` will download the Revision to your
  computer as a patch file.

* :guilabel:`Edit Related Revisions...` allows you to set the related
  Revisions.

  * :guilabel:`Edit Parent Revisions` lets you select the Revisions
    that depend, or are blocked by, this one.

  * :guilabel:`Edit Child Revisions` lets you select the Revisions that
    this one depend ons; that is, which Revisions block this one.

* :guilabel:`Edit Related Objects...` allows you to

  * :guilabel:`Edit Commits` lets you select the repository commits that are
    related to this Revision. Once we land this Revision, the commit
    that is created will be automatically associated.

  * :guilabel:`Edit Tasks` lets you select the Maniphest Tasks related to this
    Revision. This is important for associating a task with its work.

You'll also see the usual options relating to Subscribing, Tokens, and Flags.

If you scroll down to the comment box, you'll see an :guilabel:`Add Action...`
menu, which allows you to take additional actions on the task (depending
on your permissions.)

* :guilabel:`Accept Revision` marks the revision as accepted, meaning you
  (the reviewer) believe it is *ready to land*.

* :guilabel:`Request Changes` marks the revision as needing further
  modifications *before* it can be accepted.

* :guilabel:`Resign as Reviewer` removes you from the reviewers list.

* :guilabel:`Close Revision` marks an accepted revision as closed.
  *You generally shouldn't use this* - Phabricator will automatically
  close the revision as soon as the Revision's commits are landed.

* :guilabel:`Commandeer Revision` sets you as the author and owner of the
  revision. Please be courteous with this - only commandeer if you need
  to make some changes to the Revision *yourself* via Arcanist.

* :guilabel:`Plan Changes` declares your intention as the author to make
  changes to the revision. **If you're not ready for review, you should take
  this action.**

* :guilabel:`Request Review` is the opposite of *Plan Changes* - it marks the
  Revision as being ready for review.

* :guilabel:`Change Reviewers` allows you to select reviewers for the
  Revision. Note that, when you are selecting users, you can add them
  as a regular reviewer *or* as a Blocking reviewer.

* :guilabel:`Change Project Tags` allows you to select Project tags for the
  Revision.

* :guilabel:`Change Subscribers` lets you change who is subscribed to the
  Revision.

Once you've selected all of the actions you want, and written a comment
(recommended, but not required), click :guilabel:`Submit` to perform the
actions.

.. _phab_diffusion:

Diffusion
==================================

Diffusion is our repository viewer, functioning very much like GitHub in many
respects. It allows you to view the complete history of a repository -
files, commits, and all!

..  note:: The Commit Details browser is technically part of Diffusion,
    but it is important to the post-commit review workflow that Audit is
    used for, so we'll discuss it there. See :ref:`phab_audit_commit`

.. _phab_diffusion_browse:

Browsing Repositories
-----------------------------------

When you first go to Diffusion, you will be presented with a list of
our company's Git repositories. On each, you will find the following
information:

* The repository **callsign**, which starts with :code:`r` and is followed by
  one or more uppercase letters. Each repository has a unique callsign.

* The repository **name**. Clicking this will take you to the repository's
  main index.

* The **latest commit**. Click on it to view the commit information.

* The **commit count**. Click it to view the complete commit history for the
  repository.

* The repository's **Projects**. Each repository should have a department
  and project; programming repositories usually also have a team.

On the far right of the repository listing, you'll see the **date** it was
last updated, and the **:abbr:`VCS (version control software)`** the repository
uses (which is virtually always Git).

Click any repository name to view it.

.. _phab_diffusion_code:

Repository: Code
-----------------------------------

When you first view a repository in Diffusion, you will start on the **Code**
tab. At the top, you'll see the repository name, its status, and view policy.

In the upper-right corner of the page, you'll see :guilabel:`Pattern Search`.
This field allows you to search all the files in the current repository
directory using regular expressions.

Below the tabs is the repository file browser. :guilabel:`Locate File` lets you
search for a particular file or folder by name. The :guilabel:`Branch` drop-down
menu switches between repository branches.

:guilabel:`Actions` offers three actions, depending on your privileges.

* :guilabel:`Manage Repository` contains all the repository administration
  controls. In general, only Administration can use this.

* :guilabel:`View Push Logs` shows a complete history of actions taken on
  the Git repository.

* :guilabel:`Flag For Later` adds a Flag to the repository.
  (See :ref:`phab_flags`).

Perhaps the single most important button here is :guilabel:`Clone`, which
provides the URIs for the repository, as used in the :code:`git clone` command.

..  important:: Be sure you select the correct URI! The one beginning in
    :code:`ssh://` is recommended, as it uses your SSH keys to authenticate.
    See :ref:`gitarc_importrepos`.

The file browser itself lists each **filename** (click it to view the file
or enter the directory), the latest **commit message**, and the
**date of the last commit**. At the far right is the :guilabel:`History`
button, which shows the complete commit history for that file or directory.

Below the file browser is :guilabel:`Recent Commits`, which shows the last
15 or so commits to the repository. Each entry has following:

* The **Commit number**. Click it to view the commit details.

* The **Build status**, which will either show a green checkmark for "Passed",
  a red "X" for "Failed", or a grey arrow for "Building". Click the icon to see
  the build details in Harbormaster.

* The **Audit status** shows whether there are any Audits (post-commit reviews)
  on the commit. A plain black checkmark means there are no Audits. A red "X"
  means someone has raised a concern against the Commit, while a green checkmark
  means the commit has been Audited and approved.

* The **Revision number** is the ID of the Differential Revision related to
  this commit. If there is no Revision number, the code was committed to the
  repository without a Differential Revision. (Only Repository Masters can
  do this.)

* The **Author** shows the user who authored the commit.

* **Details** displays the commit message.

* **Committed** is the date the commit was published.

On the far left of the Recent Commit list is the **graph**, which shows the
relationship between commits.

Finally, at the bottom of the page, we see the **README** for the repository.

.. _phab_diffusion_code_exploring:

Exploring the Repository
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

When you select a directory or file in the repository file browser,
it will display it in a slightly different layout.

At the top is the repository name and current file path. Click on any part
of the file path to jump back to that location.

Below that is the commit number. Click on it to view the commit summary.
Next to that is the branch name.

When viewing a directory, you'll notice two buttons at the top:

* :guilabel:`Compare` allows you to compare branches and commits.

* :guilabel:`History` displays the complete commit history for the
  directory.

When viewing a file, you'll see four or five buttons:

* :guilabel:`Last Change` displays the changes made to the file during the
  last commit.

* :guilabel:`Enable Blame` (or :guilabel:`Disable Blame`) toggles the
  blame for the file. This shows who wrote what line, and what Commit
  and Revision the line was written in.

* :guilabel:`Raw File` opens the plain file. This is the button you want if
  you want to be able to download or copy/paste from the web interface.

* :guilabel:`Back to HEAD` allows you to jump to the current version of the
  file. If you're already there, this button will be hidden.

* :guilabel:`History` displays the complete commit history for the file.

..  note:: You can click on a line number to highlight it. Then, when you
    provide the URL to another person, their browser will jump to and
    highlight that line.

Below the file browser is **Owner Packages**, which shows which Packages
the directory belongs to. (See :ref:`phab_owners`.)

.. _phab_diffusion_branches:

Repository: Branches
-----------------------------------

The :guilabel:`Branches` tab displays all branches on the repository.
Each entry displays the **name** of the branch, followed by the last
**commit number**, **commit message**, and **date** of the commit.

On the right side is the **build status** (click it to see the Harbormaster
build details).

* The :guilabel:`Browse` button jumps to the repository file browser for
  that branch.

* The :guilabel:`Compare` button allows you to compare branches and commits.

.. _phab_diffusion_tags:

Repository: Tags
-----------------------------------

The :guilabel:`Tags` tab displays the tags on the repository. Each entry
displays the **version** of the tag, followed by the last **commit number**,
the **tag name**, the **author**, and the **date created.**

On the right side is the **build status** (click it to see the Harbormaster
build details).

* The :guilabel:`Browse` button jumps to the repository file browser for
  that tag.

* The :guilabel:`Compare` button allows you to compare branches and commits.

.. _phab_diffusion_history:

Repository: History
-----------------------------------

The :guilabel:`History` tab displays all the Commits on the repository.
Each box displays...

* The **date** of the commit.

* The **commit message**. Click it to view the Commit details.

* The **Commit number**, which you can click to view the Commit details.

* The **Revision number**, which takes you to the Differential Revision related
  to the Commit.

* The **author** and **timestamp** of the commit.

* The **build status** on the right, which you can click to see the Harbormaster
  build details.

* The :guilabel:`Compare` button, which allows you to compare branches and
  commits.

.. _phab_diffusion_graph:

Repository: Graph
-----------------------------------

The :guilabel:`Graph` tab also displays the complete commit history, in the same
way as on the main page of the Code tab. The graph on the left side shows you
the relationships between the commits.

.. _phab_audit:

Audit
==================================

What Is Audit?
-----------------------------------

Audit is for **post-commit reviews**. In other words, you should use this
when:

* Problem code made it into the repository.

* Something was missed in a landed Differential Revision.

* You've found the exact commit that caused a bug.

In many ways, Audit functions in largely the same way as Differential.
The one critical difference to note is that **changes cannot be made
to a Commit**! This means you will have to stay on top of any Commit
that you raise concerns on; when the problem is resolved, go back and
approve the earlier commit!

..  sidebar:: **The Rule of Audit Verification**

    If you Raise Concern on a Commit, you are responsible to *Verify* the
    concerns have been addressed; if they have, you should Accept the
    Commit you originally audited.

Audits have the following workflow:

1)  Code is committed to the Git repository by user A.

2)  User B finds a problem in the code and **Raises a Concern** against the
    Commit.

3)  User A is notified, and makes the appropriate changes in one or more
    *later commits*. They **Request Verification** on the original Commit.

4)  User B is notified of the verification request, and confirms that the
    problems are resolved in later commits. They **Accept Commit**.

Browsing Audits
-----------------------------------

By default, Audit will display a list of open Audits you are involved in:

* Needs Attention: Commits you authored that have concerned raised against them.

* Needs Verification: Commits which you previously reviewed, and which you
  now need to verify that your concerns were addressed (in later commits).

* Ready to Audit: Commits awaiting you to audit (review) them.

* Waiting on Authors: Commits you raised concerns against, and which the author
  needs to look at.

* Waiting on Auditors: Commits you authored that are pending review.

.. _phab_audit_commit:

Anatomy of a Commit
-----------------------------------

When you view a Commit, there is a lot of additional information, as well as
several actions you can take on it.

..  note:: This is technically part of Diffusion, but we cover it here because
    it is highly relevant to the Audit workflow.

.. _phab_audit_commit_details:

Description and Details
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

At the top of page is the **commit message**, which doubles as the name,
as well as the **commit number**. Below that is the **author** and
**timestamp.**

The **Description** ordinarily shows all the information from the related
Differential Revision, or else it just restates the commit message.

On the **Details** pane, we see the following:

* **Auditors**: A list of individual post-commit reviewers.

* **Group Auditors**: A list of group post-commit reviewers. A member of each
  listed project must audit the commit.

* **Committed**: Who created the commit, and when.

* **Pushed**: Who pushed the commit to the repository (``git push``), and when.

* **Reviewer**: Who reviewed the related Revision, if anyone.

* **Differential Revision**: The ID of the related Revision.

* **Parents**: The Commits this one was derived from.

* **Branches**: The branches this commit appears on.

* **Tags**: The tags this commit appears on.

* **Tasks**: The Maniphest Tasks that this commit is linked to.

* **References**: The Git reference to this commit, if any. For example, the
  latest  ``devel``  commit will have the reference ``HEAD -> devel``.

Below the Details box is the complete commit history, including build reports
and comments.

.. _phab_audit_commit_actions:

Actions
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

..  note:: The concept of Auditing is discussed in more detail in
    :ref:`phab_audit`.

On the right, there are several actions you can take, depending on permissions.

* :guilabel:`Edit Commit` allows you to modify the Auditors
  (post-commit Reviewers), Project tags, and Subscribers.

* :guilabel:`Download Raw Diff` downloads the Commit as a Git patch file.

* :guilabel:`Edit Related Objects...` lets you link the Commit to Maniphest
  Tasks and Differential Revisions.

At the bottom of the Commit, you may select any of the following actions from
the drop-down box above the comment box.

* :guilabel:`Accept Commit` marks the commit as Audited and Approved.

* :guilabel:`Request Verification` means you believe you've addressed the
  concerns raised, and would like the auditor(s) to check that the problems
  have been satisfactorily resolved.

* :guilabel:`Raise Concern` indicates that there may be problems with the
  commit, which may need to be addressed in a later commit.

* :guilabel:`Change Auditors` lets you add and remove Auditors (reviewers)
  on the Commit.

* :guilabel:`Change Project Tags` modifies the Project Tags on the Commit.

* :guilabel:`Change Subscribers` modifies the Subscribers on the Commit.

.. _phab_audit_commit_changes:

Changes
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The Commit details screen looks and acts much like that of a Differential
Revision. For complete instructions on how to use it, see
:ref:`phab_differential_anatomy`.

The **Changes** pane lists all the files that were changed. Below this,
all the changes are displayed, and inline comments can be left on the commit
in the same way as on a Differential.

.. _phab_pholio:

Pholio
==================================

Pholio is for storing, reviewing, and discussing graphics. An object in
Pholio is called a **Mock**.

.. _phab_pholio_anatomy:

Anatomy of a Mock
----------------------------------

A Mock contains one or more images, and tracks each revision made to it.

The **Title** appears at the top. This is usually a brief description of
the Mock. Below it, we see whether the Mock is **open** or **closed**.

* An open mock is still in the process of being created, reviewed, or
  modified.

* A closed mock is either accepted (finalized) or rejected.

The images appear next. The large image is whichever one has been selected
for viewing. Below it is the **image title** and **image description**.
To the right of the image title is the **Download** button and the
**Full Image** button, which allows you to view the original image file in
a new tab.

If you hover over the large image, sometimes you will see purple quote bubbles
floating over the image. These represent **Inline Comments**, which are attached
to selected areas on the image. Click a comment bubble to view the comment.

Next is the **Mock History**, which shows thumbnails of all the images in the
Mock. The number of inline comments on each image is displayed as a number in
the upper-right corner of the thumbnail. Click a thumbnail to view it full size.
You'll notice that previous revisions are also visible on separate rows as you
scroll the Mock History to the right.

Below the Mock History box, we find the the **Mock Description**, which
describes the Mock as a whole.

Finally, we see the history of all actions taken on the Mock, including the
**Comments**. In addition to comments on the whole Mock, Inline Comments are
displayed here too, alongside with a thumbnail of the image selection
associated with them. Click on a thumbnail to jump to the related full-size
image.

To the right of the Mock, we see the Tags, the list of Subscribers, and the
original creator of the Mock.

.. _phab_pholio_browse:

Browsing Mocks
----------------------------------

Because we have a *lot* of Pholio Mocks, for many different purposes,
so we automatically organize them by category and type.

When you first enter Pholio, you'll see a list of Queries on the left,
mostly organized alphabetically. These correspond to the different categories,
types, and statuses that Mocks can be tagged with.
(See :ref:`phab_pholio_organize`.)

A few other Queries are offered by default:

* Subscribed: All open Mocks which you are subscribed to. You can assume that,
  if you are subscribed, you're either the creator or a reviewer.

* Open Mocks: These are all Mocks which are still "Open".

* Authored: All Mocks which you created.

* Uncategorized [Empty Me!]: All Mocks which have not been tagged yet. If
  you find anything in here, you are encouraged to tag it appropriately.

* All Mocks: Just everything.

.. _phab_pholio_create:

Creating Mocks
----------------------------------

To create a new Mock, click :guilabel:`Create Mock` in the upper-right corner
of Pholio.

* :guilabel:`Name` should be between 1-10 words, describing the Pholio Mock
  for easier searching. It should be specific enough to set it apart from
  similar Mocks ("Annie Armadillo" would be too vague), but short enough that
  it will display nicely on search results.

* :guilabel:`Description` should contain all relevant information and notes
  about the graphics and design.

* :guilabel:`Tags` should include, at minimum, the department, status,
  category, and (if relevant) type. (See :ref:`phab_pholio_organize`.)

* :guilabel:`Subscribers` should include anyone you want to see and review
  the Pholio.

* :guilabel:`Visible To` is *very* important, as Pholio is used for a lot of
  confidential materials. We generally only need to set the Space, and should
  almost always leave the second field as :code:`All Users`.

  * Use :code:`Space S2: Staff-Only` for anything relating directly to our
    game designs, or anything else that should not be shared outside of
    the company.

  * Use :code:`Space S1: Global` for anything related to open source
    development work, including programming references and UI design work
    for open-source projects.

  * In some cases, we want to share content with trusted outside contributors
    and staff, but no one else. In this case, use :code:`Space S4: Trusted`.

* :guilabel:`Editable By` can often safely be left as "All Users".
  Alternatively, you may limit it to members of a particular :code:`[Project]`.
  This is more of a formality, as *anyone* can join a :code:`[Project]`.
  Bear in mind, if someone can't *see* Mock, they can't *edit* it.

Finally, the fun part: attaching images! You can either drag-and-drop from your
file browser to the box towards the bottom of the page, or you can click on the
box and browse for your images.

Each image has two fields:

* :guilabel:`Title` is the filename by default, although it is usually useful
  to replace this with a caption.

* :guilabel:`Description` is a more in-depth description of that particular
  image. You might include additional design notes here.

Once your images are attached, you can rearrange their order by dragging the
three lines at left of each image. You can also remove images from the Mock
by clicking the "X" in the upper-right corner of the image.

Once you have everything the way you need it, click :guilabel:`Create` at
the bottom right of the page.

.. _phab_pholio_organize:

Organizing with Mock Tags
----------------------------------

Each Mock should be tagged with a **category**:

* The :code:`Character Design [Label]` tag is for anything related to designing
  characters.

* The :code:`Environment Design [Label]` tag is for 3D environments, and
  anything related to designing them.

* The :code:`Programming [Dept]` tag is applied to all Mocks relating to the
  programming department. This includes flowcharts, UML diagrams, photographs
  of whiteboarding work, and any other graphics that relate to coding.

* The :code:`Prop Design [Label]` is for anything related to designing props.

* The :code:`UI Design [Dept]` tag is for anything related to user interface
  (UI) design, including activity design.

..  note:: If you have not already added a :code:`[Dept]` tag to the Mock,
    you should include either the :code:`Graphics Design [Dept]` or
    :code:`Mass Communication [Dept]` tag.

Mocks should usually also be tagged with a **type**:

* The :code:`Design Reference [Label]` tag is for photographs and materials
  meant as visual references, such as animal photographs for character design.

* The :code:`Model Sheet [Label]` tag is for official character design
  model sheets.


* The :code:`Sketch [Label]` tag is for pencil/pen sketches and other
  draft drawings.

* The :code:`Storyboard [Label]` tag is for official storyboards.

* The :code:`UI Mockup [Label]` is for formal layouts of user interfaces and UI
  components.

* The :code:`3D Model [Label]` tag is for rendered images from 3D models.

Finally, a Mock should be tagged with a **status**:

* :code:`Pending [Control]` is for Mocks which are ready to be reviewed.

* :code:`On Hold [Control]` is for mocks which are going to be updated
  before further review.

* :code:`Approved [Control]` is for mocks which have been accepted and
  finalized.

* :code:`Rejected [Control]` is for mocks which have been abandoned for any
  reason. These are hidden from all the other built-in Queries by default.

.. _phab_pholio_manage:

Managing Mocks
----------------------------------

Once you've created a Mock, you are responsible for keeping it up-to-date.

To the right side of the Mock are several options:

* :guilabel:`Edit Mock` allows you to edit and update the Mock.

* :guilabel:`Close Mock` (or :guilabel:`Open Mock`) closes or opens the Mock
  (obviously). Remember, an **open** Mock is still awaiting revision or
  review, while a **closed** Mock is either finalized or rejected.

* :guilabel:`Edit Related Objects...` allows you to link Maniphest Tasks
  to your Mock. You should always link Mocks to their relevant Tasks.

* :guilabel:`Subscribe` (or :guilabel:`Unsubscribe`) is very important to the
  Pholio workflow. When you are Subscribed, you should assume that (unless
  you're the creator), you're supposed to review the Mock.

* :guilabel:`Award Tokens` has been mentioned previously, but it is *especially*
  useful in Mocks, since Pholio's index displays how many tokens have been
  awarded to the Mock. Feedback this way is *usually* positive, but it can be
  used either way.

When we select :guilabel:`Edit Mock`, we can change anything about it.
This works the same way as creating a Mock does (see :ref:`phab_pholio_create`).
However, there is one major difference: **every change to the images is stored
as a new revision within the Mock**. This is designed to allow us to track
and view how a design changes over time, but you should bear this in mind before
clicking :guilabel:`Save`.

..  important:: There is no way to change or remove a particular revision - a
    new one will always be created.

.. _phab_pholio_review:

Reviewing Mocks (Comments)
----------------------------------

Since Pholio lacks a formal "reviewers" status, you should always assume that
if you are *subscribed* to a Mock and are not the creator, you are supposed
to *review* it.

When you review a Mock, you should view each image in the current revision.
To view an image, click its thumbnail in the Mock History.

If you see anything you want to comment on, simply click-and-drag on the
full-size image view to draw a selection box. A **New Inline Comment** box will
appear, and you can leave a comment. Click :guilabel:`Save Draft` to save your
comment.

..  important:: Your Inline Comments will not be published until you click
    :guilabel:`Add Comment` towards the bottom of the page.

Each draft inline comment will appear as a yellow speech bubble when you hover
over the image. It will also be listed at the bottom of the page. Clicking the
comment's image thumbnail on the list will jump to the full-size image it is
attached to.

To modify a draft comment, click on it on the full-size image. To remove it,
modify the comment and simply clear out the comment box.

You can also leave comments on the entire Mock, using the comment box towards
the bottom of the page.

Once you click :guilabel:`Add Comment`, all your comments and inline comments
will be published for everyone to see.

..  warning:: Once you've published your inline comments, you cannot edit them!

.. _phab_paste:

Paste
==================================

Snippets of text and code can be stored and shared using **Paste**. In
practice, it functions almost exactly like a pastebin service. It offers
the following features:

* Specify filename or title.

* Syntax highlighting.

* Complete edit history.

Browsing Pastes
-----------------------------------

When you first open the application, you will see a list of all Active Pastes.
Using the query system, you can search them by author, language, status
(active or archived), tags, and subscribers.

The default queries on the left also allow you to view `All Pastes`
(both active and archived) and `Authored` (pastes you created).

Anatomy of a Paste
-----------------------------------

Every Paste has a **title**, which can be one of two things: either the
*filename*, or a *proper name* describing its contents.

A Paste's **status** is either `Active` (meaning it is visible on the main
index of the Paste application) or `Archived` (hidden by default). It is
not possible to delete a Paste, only to Archive it.

On the Paste details page, below the title, status, and visibility,
you will see the **author** and **creation timestamp**.

Next are the paste contents themselves, with the line numbers displayed along
the left side. If you click a line number, it will highlight that line and
modify the URL; sharing this URL will highlight and jump to the selected line.

Below the paste contents themselves is the complete item history, including
all edits and mentions. You can click :guilabel:`Show Details` next to any
edit to see the complete changes made at that time.

On the right of the page are three important actions:

* :guilabel:`Edit Paste` allows you to modify the paste and its properties.

* :guilabel:`Archive Paste` changes the paste status from Active to Archived;
  :guilabel:`Activate Paste` changes it to Active again.

* :guilabel:`View Raw File` displays the paste as a straight text or code
  file, using the title as its filename.

As always, you can comment on the object using the comment box at the bottom
of the page and clicking :guilabel:`Nom Nom Nom Nom Nom` (formerly
:guilabel:`Eat Paste`, because Phabricator is funny like that.)

Creating or Editing a Paste
-----------------------------------

You can create a new Paste by clicking :guilabel:`Create Paste` in the
upper-right corner of the application's initial page. After creation, you
can always edit a Paste by clicking :guilabel:`Edit Paste` on the Paste's
details page. Both forms look and act the same.

* The :guilabel:`Title` is typically the filename of the new Paste (recommended),
  or else it is a proper name. You can call it whatever you like, but if the
  Paste is viewed as a Raw File, the Title will be used as its filename.

* :guilabel:`Language` allows you to select the syntax highlighting you want
  to use on the post. If you leave this blank, it will try and figure it out
  automatically based on the filename in Title; otherwise, it will fall back on
  `Plain Text`.

..  note:: This uses Pygments, so not all possible languages are present.
    Ratscript is absent right now, although we *will* be adding it at a later
    date.

* :guilabel:`Text` is the actual text of the Paste.

..  note:: Line wrapping is always automatically applied when the Paste is
    displayed, although it will not modify the raw file.

* :guilabel:`Visible To` allows you to set the visibility on your Paste.
  In most cases, it is sufficient to set the Space in the first field and
  leave the second field as :guilabel:`All Users`.

* :guilabel:`Editable To` allows you to control who can edit the Paste. This
  may be useful if you're concerned about important information being changed
  or removed accidentally (or maliciously).

..  note:: Remember, all changes to the Paste will be tracked, so you can
    always revert unwanted modifications.

* :guilabel:`Tags` is useful for tagging the related departments, projects,
  and technologies (via `Labels`).

* :guilabel:`Subscribers` is the list of users who will be notified about
  changes to this Paste.
