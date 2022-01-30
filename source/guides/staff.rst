.. _gstaff:

Guide: Staff Introduction
#########################################

Welcome to MousePaw Media! We're really excited to have you aboard.
This tutorial will help you get started. If you need additional help, contact
your internship supervisor.

This tutorial will also get you started as a MousePaw Media employee.
Read through it in order. Watch out for...

..  important:: These are things you should do now.

..  warning:: These are things you need to watch out for.

.. _gstaff_policies:

Employee Handbook
=======================================

Our Employee Handbook outlines all the company policies, rules, and
expectations for staff at MousePaw Media. You should be familiar with
the latest version of these policies at all times.

We publish the latest version of the Employee Handbook at
`eco.mousepawmedia.com <https://eco.mousepawmedia.com>`_. Click
:guilabel:`Employee Handbook` to view or download.

..  important:: Download the latest copy of the Employee Handbook from
    that link, and save it to your work computer for quick reference.
    Read it through now.

You will be emailed whenever a new version of the Employee Handbook is
published.

.. _gstaff_tech:

Technical Requirements
=======================================

You **must** have Linux, ideally an Ubuntu-based distro (version 20.04
or later), installed on your computer. See :doc:`../setup_linux/install` for
instructions on installing it. If you need help, contact Jason C. McDonald.

Although we work on Linux, you may access the Staff Network via Windows, macOS,
iOS (Apple) mobile devices, Android mobile devices, or BSD-based operating
systems if you need to.

.. _gstaff_security:

Security
=======================================

It is your responsibility to keep your MousePaw Media account secure!
Here are some very important things to keep in mind at all times.

..  warning: Read this section carefully!

* Never share your password with *anyone*. We won't ask for it. Period.

* Don't leave your work devices unlocked and unattended.

* Consider enabling full-disk encryption when you install Linux.

* Use a secure password manager like `Bitwarden <https://bitwarden.com/>`_.
  Never use your browser's "save password" feature.

* Add Multi-Factor Authentication (MFA) to your Phabricator and Nextcloud accounts.
  Instructions are included in those sections below. We strongly recommend
  using the Microsoft Authenticator on your mobile and/or the
  `Authenticator app <https://flathub.org/apps/details/com.belmoussaoui.Authenticator>`_
  on Linux desktop.

* Consider adding Multi-Factor Authentication to your Mattermost account.

* Be careful clicking on unusual or disguised links in emails, even if
  the email looks like it's from within the company or from a coworker.
  Verify URLs and SSL certificates before entering your password or other
  information.

* If you lose a device you've used to access your MousePaw Media account, or
  which you use for MFA, contact us **immediately!**

If you lose access to your MousePaw Media account at any point, contact
``eco@mousepawmedia.com``. We will work with you *over video chat* (for
authentication purposes) to restore access to your account.

.. _gstaff_network:

Staff Network
=======================================

The Staff Network consists of seven major components:

* SOGo [Email/Calendar] (mail.mousepawmedia.com)

* Kimai (time.mousepawmedia.com)

* Phabricator (phab.mousepawmedia.com)

* Mattermost (chat.mousepawmedia.com)

* Nextcloud (cloud.mousepawmedia.com)

* Jenkins (ci.mousepawmedia.com)

* Etherpad (pad.mousepawmedia.com)

* LimeSurvey (survey.mousepawmedia.com)

We also use `Jitsi Meet <https://meet.jit.si/>`_ for regular communication.

Visit **staff.mousepawmedia.com** for our main staff portal, with links to
everything else.

.. _gstaff_network_email:

Email and Calendar
------------------------------------------

As with any job, email and calendar are essential. You were issued a company
email address, which comes with a calendar and a contact book.

..  warning:: Be sure to check your company email every single day, and
    respond in a timely fashion!

You can check your email and calendar through the SoGO web client.
This is useful if you don't want to configure an external client, or need
to check your email without access to your usual device.

.. _gstaff_network_email_sogosetup:

SoGO Setup
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

SoGO is a modern web client with many useful features.

First, go to mail.mousepawmedia.com/sogo. Sign in using your full company email
address and your password.

..  important:: Access your company email through the SoGO client.

You'll want to start by setting up your default signature. Tap the gear in
the upper-left to open Settings, and then go to :guilabel:`Mail` and
:guilabel:`IMAP Accounts`. Click the pencil next to your email address on
that window, and click on your name under the :guilabel:`Identity`
section.

Under :guilabel:`Signature`, add your signature. We recommend this template
for your signature:

..  code-block:: text

    Your Name Here
    Position, MousePaw Media

    Visit Us Online: MousePawMedia.com
    Twitter: @mousepawmedia

Press :guilabel:`OK` to save. Then, tap the green save icon in the upper-right
corner to save your settings. If you wish, you can take a moment to look
through your other settings.

..  important:: Set up your signature now.

.. _gstaff_network_email_sogocalendar:

SoGO Calendar
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

To navigate around SoGO, in the upper-right, click the Mail, Calendar, and
Contacts icons.

The first time you click the Calendar, it will create a Personal Calendar
for you. Any events you RSVP to will appear here. You can click the three
dots to change the name and color of this calendar.

You can also subscribe to other people's calendars.

.. _gstaff_network_email_settings:

Mail Settings
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

You can access a number of advanced settings for your email account by going
directly to `mail.mousepawmedia.com <https://mail.mousepawmedia.com>`_ and
logging in with your full email address and password.

Some things you can do from here include:

* Viewing login history (from :guilabel:`Mailbox -> General`)

* Adjust additional mailbox settings under :guilabel:`Mailbox -> Settings`.

* Setting up temporary email aliases, which are useful when you need to
  provide an email address for something, but want to be able to delete it
  if it starts to receive spam.

* Control spam settings, whitelist/blacklist email addresses.

* Generate app passwords for IMAP/SMTP/CalDAV/CardDAV logins without exposing
  your company email address.

* Check the intercepted spam in Quarantine, and take action on it (including
delivering to mailbox) by clicking :guilabel:`Show item`,
scrolling down, and clicking :guilabel:`Actions`.

You can also jump right to SOGo from here via the :guilabel:`Login to webmail`
button under :guilabel:`Mailbox`, or from the :guilabel:`Apps` menu at
upper-right.

.. _gstaff_network_email_imap:

IMAP/SMTP Access
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

You can also connect your account to an external email client, such as
Thunderbird or your mobile device.

You can find full configuration instructions by going to
`mail.mousepawmedia.com <https://mail.mousepawmedia.com>`_, going to
:guilabel:`Mailbox -> General`, and scrolling down to the
:guilabel:`[Show configuration guides for email clients and smartphones]`
link. That will show customized instructions for your email account and
our servers.

In short, here are the recommended server settings:

- Username: (Your full company email address.)

- Password: (Your company password.)

- Incoming Mail: IMAP (recommended)

  - Server Name: ``mail.mousepawmedia.com``

  - Port: ``993``

  - Connection security: ``SSL/TLS``

  - Authentication method: ``Normal password``

- Outgoing Mail: SMTP

  - Server Name: ``mail.mousepawmedia.com``

  - Port: ``465`` (recommended) or ``587``.

  - Connection security: ``SSL/TLS``

  - Authentication method: ``Normal password``

..  important:: Connect your company email to an email client, such as
    Thunderbird, Evolution, or your smartphone.

.. _gstaff_network_email_caldav:

CalDAV Access
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If you use an external calendar application, you can work directly with your
MousePaw Media calendar via CalDAV.

The easiest way to get your CalDAV link is to go to
`SoGO <https://mail.mousepawmedia.com/sogo>`, tap the Calendar icon in
the upper-right corner, and click the three dots next to the calendar you want
to access via CalDAV. Select :guilabel:`Links to this Calendar`.

Depending on whether your client uses CalDAV or WebDAV, select the appropriate
link under :guilabel:`Authenticated User Access`. Your calendar application
must support providing a username and password for this to work.

If you are using a calendar application that doesn't support authentication on
CalDAV or WebDAV, you can also use the Public Access link. You can control
public access permissions by clicking the three dots next to your calendar
and selecting :guilabel:`Sharing...`. You can provide either full or
time/date-only read access for the three types of events.

..  note:: If you use Android, the easiest way to work with CalDAV is to use
    the DAVx5 application. It's open source. You can download it for free from
    FDroid, but please consider buying it from the Google Play store to help
    support the creators.

..  important:: Set up your favorite calendar app with access to your calendar
    via CalDAV, so you can receive notifications of upcoming events.

.. _gstaff_network_email_tasks:

Regular Email Tasks
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* Correspond with co-workers. Read and respond to messages in a timely fashion.

* Schedule and RSVP for meetings.

* Contact ECO to schedule time off, ask questions, and report concerns.

.. _gstaff_network_kimai:

Kimai
-----------------------------------

Kimai is where we track time. You are responsible for reporting your time
**every day**. Each day's time sheets are locked automatically the next morning
at 10 am.

..  warning:: If you forget to log your time, you will NOT be reminded.
    Contact ``eco@mousepawmedia.com`` if you forget, and report your time
    that way.

To log your time, go to ``https://time.mousepawmedia.com``. Login using your
company username and password.

You will be on the :guilabel:`My times` section by default.

**We recommend creating separate entries for each major task you work on.**
This will make it easier for you to see how you use your time.

To add time, click the large Play button in the upper-right corner of the page.
Fill out the following fields:

* :guilabel:`From`: select the start date and time for your entry.
  You may estimate the start the time if you can't remember it.

* :guilabel:`Duration`: Enter the duration in ``H:MM`` format.

* :guilabel:`Project`: Select ``Internal``.

* :guilabel:`Activity`: Select the project you were working on.

  * Most MousePaw Media projects have an entry here. If you don't see the one
    you need, use ``General`` and contact your supervisor.

  * Use ``Research`` for general-purpose research and training.

  * Use ``General`` for meetings, internship assignments, and anything not
    covered by another category.

  * Use ``DevOps/IT`` for repository master, build system, and server work.

  * Use ``Operations`` for management, administrative, and standards board tasks.

  * Use ``Hiring`` for hiring-related tasks.

* :guilabel:`Description`: When relevant, you **must** include appropriate
  Phabricator object codes, including Maniphest Tasks, Differential Revisions,
  Ponder Questions, wiki pages, and the like.

..  warning:: These notes are not a replacement for your Dev Journal entries.

Finally, click :guilabel:`Save` in the lower-left corner to store the hours.

While you're here, take a look at the :guilabel:`Dashboard`,
:guilabel:`Calendar` and :guilabel:`Reporting` tabs. These give you detailed
breakdowns of your hours, including what you worked on and when you worked.

If you need a tool to help you track your time, check out the
`Timecard <https://codemouse92.github.io/Timecard/>`_ application, created by
Jason C. McDonald.

Regular Kimai Tasks
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* Log your hours *every day you work*.

* Seriously, that's it. **Log your time!**

.. _gstaff_mattermost:

Mattermost
----------------------------

We use Mattermost, an open source chat service, for team chat.
**All staff members are expected to be logged into Mattermost during work**,
and are strongly encouraged to stay signed in when convenient to facilitate
collaboration and communication.

Mattermost can be accessed directly through the web browser from
`chat.mousepawmedia.com <https://chat.mousepawmedia.com/>`_, but we
*strongly* recommend installing the official client, which works on Linux,
Windows, macOS, Android, and iOS. You can download and install the client from
`mattermost.com/download <https://mattermost.com/download/>`_.

..  important:: Install the Mattermost client on your work computer, and if
    possible, on your mobile device.

.. _gstaff_mattermost_login:

Creating Your Account
^^^^^^^^^^^^^^^^^^^^^^^^^^

Mattermost is the only service we use which is not tied to your MousePaw ID.
Instead, you'll need to create an account using the invite that was sent
to your MousePaw Media email address. You **must** sign up using your
company email address, or else you will be unable to access our chatrooms.

..  important:: Sign up using the invite that was emailed to your company
    email address.

Once you've signed in to Mattermost, either through the website or the client,
you should immediately set up your user profile. Click your profile picture in
the upper-left corner and click :guilabel:`Account Settings`. Add your
:guilabel:`Full Name`, your :guilabel:`Profile Picture`, and anything else you
want to add.

..  important:: Log into the MousePaw Media Mattermost and set up your user
    profile with your Full Name and Profile Picture.

At this time, you may want to take a minute and look through the other
Account Settings, especially...

* Security: Multi-factor Authentication
* Notifications: Email Notifications
* Display: Theme

.. _gstaff_mattermost_rooms:

Rooms in Mattermost
^^^^^^^^^^^^^^^^^^^^^^^^^^^^

When you log in for the first time, you'll be added to the most important
rooms. There may be more that you can find and join freely later.

Here's the most important rooms for you to join and know:

**Town Square** (``~town-square``) is our public room, linked to our IRC
channel. Please default to using this room for any development conversations,
so anyone joining via IRC can see and join in.

..  warning:: Because Town Square is bridged to IRC, deleting a message will not
    actually delete it from the room history altogether. What you say, you
    cannot take back.

**Water Cooler** (``~water-cooler``) is our staff-only chatroom. Use this for
any internal conversations that we don't want to have in public.

**Off Topic** (``~off-topic``) is for random chat-chat that doesn't really
relate to work.

**ECO** (``~eco``) is for any questions you want to bring up with ECO, but
which you don't necessary need to keep confidential from other staff.
(If you want a private conversation, feel free to email
``eco@mousepawmedia.com`` instead.)

**IT**  (``~it``) is for reporting any problems with the staff network,
including email.

Last, but not least, you can private message anyone through Mattermost.

..  important:: Post a message in ``~town-square``.

.. _gstaff_network_phab:

Phabricator
--------------------------------------

Phabricator is where most of our development work takes place. It hosts our
repositories, task tracker, knowledge base, and wiki.

Phabricator is MASSIVE, so which apps you use depend heavily on what you're
doing. There are six major apps you should be making frequent use of:

* Phame: Development journals

* Maniphest: Issue tracking

* Projects: Sprint planning (Workboards)

* Differential: Code review

* Phriction: Wiki

* Ponder: Question & Answer

* Pholio: Graphics review

All of these apps (and more) are on the left side of the main page of
Phabricator.

.. _gstaff_network_phab_settings:

Adjusting Settings
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

To get the most out of Phabricator, you should adjust some settings
and fill out your profile. Follow these instructions...

1.  Log into Phabricator using your company (LDAP) credentials.

2.  Click your profile picture in the upper-right corner.

3.  On that page, click :guilabel:`Manage` and :guilabel:`Edit Profile`.

4.  Fill out as much of the profile as you want/can. Have fun with this!

..  note:: Please fill out at least five "Fun Stuff" fields, as this is what
    we will use to craft your profile on the MousePaw Media website.

5.  Click :guilabel:`Save Profile` at the bottom.

6.  Click :guilabel:`Edit Settings` at right. Alternatively, click your icon
    picture at the top of the page and select :guilabel:`Settings`.

7.  Select :guilabel:`Account` at left, and set :guilabel:`Pronoun`
    appropriately. Click :guilabel:`Save Changes`.

8.  Click :guilabel:`Notifications` at left. Select the option
    :code:`Web and Desktop`, and save. Then, click
    :guilabel:`Enable Desktop Notifications`. You may consider clicking
    :guilabel:`Send Test Notification` in the upper-right corner to test.
    Then, click :guilabel:`Save Preference`.

9. Click :guilabel:`External Accounts` at left. Add your GitHub account.
    Click :guilabel:`Save Changes`.

..  important:: You should also add your company email address to your GitHub
    account, so you can get public credit for your contributions to our
    repositories.

..  sidebar:: Notifications vs. Emails

    All staff members are expected to check Phabricator frequently. Email
    notifications are a great way to remind you to do this, but they can also
    get quite overwhelming!

    If you choose to set any notifications to "Notify" instead of "Email",
    you should ensure you are *already* in the habit of checking Phabricator
    at the start of each workday.

    A great way to be notified about things while you're working is to leave
    Phabricator open in a browser tab. This way, you'll get a handy popup
    whenever something important occurs. Otherwise, you can check missed
    notifications from the Bell menu in the upper-left corner of Phabricator.

10. We **strongly recommend** adding Multi-Factor Autentication to your
    Phabricator account. Click :guilabel:`Multi-Factor Auth` at left.
    On your mobile device, install a trustworthy authenticator app like
    Microsoft Authenticator. On your work laptop, you can install
    `Authenticator <https://flathub.org/apps/details/com.belmoussaoui.Authenticator>`_.
    ONLY USE ONE! (Using both requires both.) On Phabricator, click
    :guilabel:`Add Auth Factor`. Follow the instructions to link your
    authenticator app. Repeat for the other app.

1.   Click :guilabel:`Email Preferences` at left. Here, you may shut off many
    email notifications by selecting the :guilabel:`Notify` option for any
    given item. Recommended defaults are provided, but you can adjust these
    to your needs.

..  warning:: Do NOT select "Ignore" for any notifications! All notifications
    are ultimately controlled by whether you're "Subscribed" to an object.

1.  Take a few minutes to go through the rest of the settings independently.
    Use the menu at left to see more settings.

2.  Click :guilabel:`Phabricator` in the upper-left corner to return to the
    main page.

.. _gstaff_network_phab_phame:

Phame
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Phame is our internal blogging platform, which we use for our
company newsletter and our Dev Journals.

Read **The Check-In** every week for the latest reminders, company news,
and helpful tips.

Your personal Dev Journal is your weekly log of what you're building and
learning. Since we're a globally distributed, remote team, this how we share
progress with one another and with our supervisors. This journal is visible
only to staff and trusted community members.

You must update your Dev Journal by Monday morning every week, with the
following information:

  * What you've done over the past week,
  * What challenges you faced over the past week,
  * What you learned over the past week,
  * What you're planning to do over the upcoming week,
  * The Phabricator object IDs or links for anything you've worked on (inline).

..  warning:: Your Dev Journal is how we track your job attendance. Be sure
    to post it **every single week**! If it is not posted on time, your hours
    for the week might not be counted.

Along with posting your own Dev Journal entry, read and comment on **at least**
two other team member's dev journal posts every week.

For more information on Phame, see :ref:`phab_phame`.

Regular Phame Tasks
""""""""""""""""""""""""""""""""""""""

* Read "The Check-In" for the week.

* Post your weekly entry to your own Dev Journal.

* Read and comment on at least two other new Dev Journal entries.

.. _gstaff_network_phab_maniphest:

Maniphest
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

..  sidebar:: Rule of Task Creation

    Unless the goal will be completed in the next ten minutes, **MAKE A TASK** on Maniphest.

Maniphest is our issue tracker. Just about everything you work on should
have an associated Maniphest task, which you keep up to date as you work.
Bigger tasks should be broken down into smaller subtasks.

You should also use the comments section of a Maniphest task to take notes
and discuss issues with your teammates.

For more information on Maniphest, see :ref:`phab_maniphest`.

Regular Phame Tasks
""""""""""""""""""""""""""""""""""""""

* Create and update tasks for everything you're working on.

* Report bugs and request features.

.. _gstaff_network_phab_projects:

Projects
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The Projects app does a lot of things, but perhaps most importantly, it hosts
our Kanban boards for sprint planning and tracking. Maniphest tasks are tagged
with Project tags, and then they appear on the Workboard for that project.

To see the Workboard for a project, go to Projects, click the project in
question, and click :guilabel:`Workboard`. Be careful about moving tasks
between columns, however, as that's something we do as a group as part of
the sprint process.

For more information on Maniphest, see :ref:`phab_projects`.

Regular Projects Tasks
""""""""""""""""""""""""""""""""""""""

* See what tasks are selected for the current sprint on a project.

.. _gstaff_network_phab_phriction:

Phriction
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Our central knowledge store and wiki is Phriction. This is where we keep
all of our project specs, design notes, and idea documents. We also track
our sprints here.

Phriction is an excellent place to share knowledge of all sorts. If you
learn something useful that might be helpful to someone else, just add it!

With rare exception, anyone can edit anything on the wiki. It's fully version
tracked, so don't be afraid to make changes. The number one rule of wiki use:
don't ask permission! Just do it.

For more information on Phriction, see :ref:`phab_phriction`.

Regular Phriction Tasks
""""""""""""""""""""""""""""""""""""""

* Monitor pages for projects you're involved in.

* Maintain any specs and design notes you're responsible for.

* Learn and share knowledge, especially via the Resources section.

.. _gstaff_network_phab_differential:

Differential
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

All code and text-based content that is being developed is tracked,
reviewed, and discussed on Differential. Any code you write will be
submitted here first to be reviewed.

If you've used GitHub before, this is our equivalent of a Pull Request.

For more information on Differential, see :ref:`phab_differential`.

Regular Differential Tasks
""""""""""""""""""""""""""""""""""""""

* Submit and maintain Revisions for your code revisions.

* Review other people's Revisions.

.. _gstaff_network_phab_ponder:

Ponder
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Ponder is like our own personal StackOverflow. You can ask and answer questions
here. While Mattermost is highly recommended for quick questions and live
conversations, Ponder is fantastic for creating a team knowledge base and
solving problems asynchronously.

For more information on Ponder, see :ref:`phab_ponder`.

Regular Ponder Tasks
""""""""""""""""""""""""""""""""""""""

* Ask questions.

* Store collected information as you research a problem.

* Help answer other people's questions.

.. _gstaff_network_phab_pholio:

Pholio
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Graphical assets, including most work produced by the Design+Production
team, are uploaded to Pholio for review and discussion. We also use Pholio
for user interface mockups, as well as flowcharts and diagrams from the
Programming team.

A single Pholio Mock can contain multiple images, and multiple revisions
thereof. Comments can be left directly on the images themselves, as well as
in the comments section.

For more information on Pholio, see :ref:`phab_pholio`.

Regular Pholio Tasks
""""""""""""""""""""""""""""""""""""""

* Submit and maintain Mocks for your graphical work.

* Review other people's Mocks.

.. _gstaff_nextcloud:

Nextcloud
----------------------------

Nextcloud is where we store all important staff documents
(see :ref:`gstaff_eco`), collaborate on documents, and share a lot of common
non-code files.

One essential function of Nextcloud is to allow us to collaborative edit our
sprint plan and sprint retrospective documents.

Additionally, if you work in the Design+Production or Content Development
departments, you'll upload most of your work to Nextcloud.

First Steps
^^^^^^^^^^^^^^^^^^^^^^^^^^^

1.  When you first log into Nextcloud, click your username in the upper-right
    corner and select :guilabel:`Settings`. This will take you to your profile
    and settings screen. Fill out your profile.

2.  At left, select :guilabel:`Security`. Start by saving your backup codes in
    case you lose your device. Then, enable both
    :guilabel:`Nextcloud Notification` and :guilabel:`Enable TOTP`. That
    last one will walk you through linking to *one* Authenticator app; we
    recomemend Microsoft Authenticator on your mobile device. If you don't
    have a mobile, use `Authenticator on your Linux machine <https://flathub.org/apps/details/com.belmoussaoui.Authenticator>`_.

3.  The Activity section allows you to customize notifications. We recommend
    leaving many Push notifications enabled.

If you'll be using Nextcloud regularly, you may consider setting up the
Nextcloud Client on your computer. See :ref:`nextcloud_client`.

Regular Nextcloud Tasks
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* Collaborative edit documents, especially sprint plans, sprint retrospectives,
  and standards drafts.

* Upload work. (Content Development, Design+Production)

* Review and proofread content files. (Content Development)

When you upload files meant to be shared, be sure to place them in the
appropriate shared folder.

.. _gstaff_eco:

ECO: Employee Care and Opportunity
=======================================

Our "human resources" department is called **ECO**, which stands for
*Employee Care and Opportunity*.

ECO Forms
---------------------------------------

All the ECO forms you'll need are stored on Nextcloud, in the *ECO* folder.

All forms must be filled out, signed *by hand* (including by mouse or tablet),
and emailed to ``eco@mousepawmedia.com``. You can do this either by printing
and scanning the document, or digitally with
`Xournal++ <https://github.com/xournalpp/xournalpp>`_.

* **Formal Grievance**: If you are unable to resolve a conflict with a co-worker
  via informal discussions, you may file this form within 15 days of the
  most recent incident.

* **Promotion Request**: When you are ready to be graduate from the internship
  program, or otherwise be promoted to a higher seniority, you must fill out
  and submit this form. Interns will also need the appropriate
  **Internship Checklist**.

* **Leave of Absence Request**: Any time you will be absent for a week or more,
  or under six hours a week in the case of an intern, you must file this
  request at least two days before your absence!

* **Resignation Request**: If you choose to leave MousePaw Media, you must
  file a resignation request. If you're an intern, we may choose to terminate
  your employment with us instead of accepting the resignation, as specified
  in your contract.

Management Forms
--------------------------------------------

There are a few more ECO forms which are accessible only to management.

* **Hiring Checklist**: When we are reviewing an applicant for our internship
  program, we use this form to collect and track all the relevant information
  about them.

* **Employee Disciplinary Warning Notice**: For serious and/or recurring
  problems, a supervisor may detail the incident and the expected remedy
  using this form. If you receive one, be sure to read it, initial and sign
  it, and send it back via e-mail ASAP.

* **Employee Termination**: In the rare and unfortunate case where an employee
  must be fired, we use this form. There is also a separate
  **Internship Termination** form.

Next Steps
===========================================

Previous MousePaw Media graduates have written up some tips for new interns!
You can read those on the Phabricator Phriction wiki at the link below:

..  important:: Read `Internship Tips <https://phab.mousepawmedia.com/w/resources/internship_tips/>`_

You can learn more about the different parts of the Staff Network in the
other sections of this documentation.

If you're an intern, you can find a list of all your assignments on
the appropriate Internship Checklist at the bottom of the
`Assignments Phriction page <https://phab.mousepawmedia.com/w/assignments/>`_.

Programmers should check out these sections next:

* :ref:`genv`
* :ref:`grevision`
* :ref:`gbuild`

Content Developers should check out this section next:

* :ref:`genv_content`

Design+Production and Mass Communication staff should check out this
section next:

* :ref:`genv_designprod`
