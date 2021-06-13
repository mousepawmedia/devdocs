.. _gstaff:

Guide: Staff Introduction
#########################################

Welcome to the MousePaw Media family! We're really excited to have you aboard.
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

You **must** have Linux, ideally an Ubuntu-based distro (version 18.04
or later), installed on your computer. See :doc:`../setup_linux/install` for
instructions on installing it. If you need help, contact Jason C. McDonald.

Although we work on Linux, you may access the Staff Network via Windows, Mac,
iOS (Apple) mobile devices, Android mobile devices, or BSD-based operating
systems if you need to.

.. _gstaff_network:

Staff Network
=======================================

The Staff Network consists of seven major components:

* Email (webmail.mousepawmedia.com or IMAP)

* Kimai (time.mousepawmedia.com)

* Phabricator (phab.mousepawmedia.com)

* Nextcloud (cloud.mousepawmedia.com)

* Jenkins (ci.mousepawmedia.com)

* Etherpad (pad.mousepawmedia.com)

* LimeSurvey (survey.mousepawmedia.com)

We also use Skype for regular communication.

Visit **staff.mousepawmedia.com** for our main staff portal, with links to
everything else.

.. _gstaff_network_email:

Email
------------------------------------------

..  note:: Email is hosted on Webster, and thus is unaffected by the status of Hawksnest.

Email is one of our primary ways of communicating at MousePaw Media. You have
a company email address issued to you.

..  warning:: Be sure to check your company email every single day, and
    respond in a timely fashion!

You can check your email through the Roundcube or Nextcloud web clients.
This is useful if you don't want to configure an external client, or need
to check your email without access to your usual device.

.. _gstaff_network_email_roundcube:

Roundcube
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Roundcube is a modern web client with many useful features. It is on
Webster, and thus is unaffected by Hawksnest/DevNet outages.

1.  Go to webmail.mousepawmedia.com. Sign in using your full company email
    address and your password.

2.  The first time you log in, you'll be prompted to create your user identity.
    Fill in your Display Name (full name with middle initial preferred),
    set your Organization to ``MousePaw Media``, and add a default signature.

    We recommend this template for your signature:

..  code-block:: text

        Your Name Here
        Position, MousePaw Media

        Visit Us Online: MousePawMedia.com
        Twitter: @mousepawmedia

That's all! Now you can begin browsing your email.

..  important:: Access your company email through the Roundcube client.

.. _gstaff_network_email_nextcloud:

Nextcloud
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If you find yourself using Nextcloud a lot, you may prefer to use that as
your email client.

1.  Go to cloud.mousepawmedia.com. Sign in using your company username
    and password. Click the Email icon on the top toolbar.

2.  Set up your signature. Click the three dots next to your email account
    name and click :guilabel:`Account Settings`. Under Signature, enter
    your signature.

    Here's the example signature:

..  code-block:: text

        Your Name Here
        Position, MousePaw Media

        Visit Us Online: MousePawMedia.com
        Twitter: @mousepawmedia

3. Click :guilabel:`Save Signature`.

4. Adjust other settings as you see fit.

..  important:: Access your company email through the Nextcloud client.

.. _gstaff_network_email_imap:

IMAP Access
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

You can also connect your account to an external email client, such as
Thunderbird or your mobile device. Here are the server settings:

- Username: (Your full company email address.)

- Password: (Your company password.)

- Incoming Mail: IMAP (recommended)

  - Server Name: ``mail.mousepawmedia.com``

  - Port: ``993``

  - Connection security: ``SSL/TLS``

  - Authentication method: ``Normal password``

- Incoming Mail: POP3

  - Server Name: ``mail.mousepawmedia.com``

  - Port: ``995``

  - Connection security: ``SSL/TLS``

  - Authentication method: ``Normal password``

- Outgoing Mail: SMTP

  - Server Name: ``mail.mousepawmedia.com``

  - Port: ``465`` (recommended) or ``587``.

  - Connection security: ``SSL/TLS``

  - Authentication method: ``Normal password``

..  important:: Connect your company email to an email client, such as
    Thunderbird, Evolution, or your smartphone.

.. _gstaff_network_email_tasks:

Regular Email Tasks
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* Correspond with co-workers. Read and respond to messages in a timely fashion.

* Send your check-in email on the same day each week, detailing:

  * What you've done over the past week,
  * What you learned over the past week,
  * What challenges you faced over the past week,
  * What you're planning to do over the upcoming week,
  * The Phabricator object IDs or links for anything you've worked on (inline),
  * Any questions you have for your supervisors.

..  important:: Log into your email and send an message to
    :code:`eco@mousepawmedia.com`. State what day you would like your weekly
    check-in email to be on.

..  warning:: Your check-in email is how we track your job attendance. Be sure
    to send it on your regular day **every single week**! If it is not received
    on time, your hours for the week might not be counted.

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

* :guilabel:`Description`: You **must** include appropriate Phabricator object
  codes, including Maniphest Tasks, Differential Revisions, Calendar Events,
  Ponder Questions, wiki pages, and the like.

..  warning:: These notes are not a replacement for your check-in email.

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

.. _gstaff_network_phab:

Phabricator
--------------------------------------

Phabricator is where most of our development work takes place. It hosts our
repositories, task tracker, knowledge base, and wiki.

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

11. Click :guilabel:`Email Preferences` at left. Here, you may shut off many
    email notifications by selecting the :guilabel:`Notify` option for any
    given item. Recommended defaults are provided, but you can adjust these
    to your needs.

..  warning:: Do NOT select "Ignore" for any notifications! All notifications
    are ultimately controlled by whether you're "Subscribed" to an object.

12. Take a few minutes to go through the rest of the settings independently.
    Use the menu at left to see more settings.

13. Click :guilabel:`Phabricator` in the upper-left corner to return to the
    main page.

Regular Phabricator Tasks
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Phabricator is MASSIVE, so which apps you use depend heavily on what you're
doing. There are six major apps you should be making frequent use of.

* Phame

  * Read 'The Check-In' every week. You'll find reminders, company news,
    helpful tips, and Jason McDonald's "Useless Trivia of the Week".

* Calendar (see :ref:`phab_calendar`)

  * RSVP for all events you're invited to.

  * Create events you're organizing.

..  sidebar:: Rule of Task Creation

    Unless the goal will be completed in the next ten minutes, **MAKE A TASK** on Maniphest.

* Maniphest (see :ref:`phab_maniphest`)

  * Create and manage tasks for everything you're working on.

  * Report bugs and request features.

* Phriction (see :ref:`phab_phriction`)

  * Monitor pages for projects you're involved in.

  * Maintain any specs and design notes you're responsible for.

  * Learn and share knowledge, especially via the Resources section.

* Ponder (see :ref:`phab_ponder`)

  * Ask questions.

  * Store collected information as you research a problem.

  * Help answer other people's questions.

* Differential (see :ref:`phab_differential`)

  * Submit and maintain Revisions for your code revisions.

  * Review other people's Revisions.

* Pholio (see :ref:`phab_pholio`)

  * Submit and maintain Mocks for your graphical work.

  * Review other people's Mocks.

All of these apps (and more) are on the left side of the main page of
Phabricator.

.. _gstaff_nextcloud:

Nextcloud
----------------------------

Nextcloud allows us to share and collaboratively edit documents.

First Steps
^^^^^^^^^^^^^^^^^^^^^^^^^^^

1.  When you first log into Nextcloud, click your username in the upper-right
    corner and select :guilabel:`Personal`. This will take you to your profile
    and settings screen.

2.  If you scroll down a little, you will see buttons for downloading the Nextcloud
    client for various platforms. Now would be a good time to set up one or more
    up. To install the client, see :ref:`nextcloud_client`.

3.  The Activity section allows you to customize notifications. You should leave
    most Stream options checked, so you'll know when things happen on Nextcloud.
    However, you may want to uncheck some Mail options, to keep email to a
    minimum.

4.  Uncheck the boxes labeled :guilabel:`List your own file actions in the stream`
    and :guilabel:`Notify about your own actions via email`, so you don't
    receive notifications about your *own* actions.

If you'll be using Nextcloud regularly, you may consider setting up the
Nextcloud Client on your computer. See :ref:`nextcloud_client`.

Regular Nextcloud Tasks
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Nextcloud is where we store all important staff documents
(see :ref:`gstaff_eco`), and where we share a lot of common non-code files.

If you work in the Design+Production or Content Development departments,
you'll especially be spending a lot of time on Nextcloud.

* Upload files.

* Review and proofread files. (Content Development)

* Collaborate on documents. (Content Development)

When you upload files, be sure to place them in an appropriate folder and
**share the folder with your department.**

.. _gstaff_eco:

ECO: Employee Care and Opportunity
=======================================

Our "human resources" department is called **ECO**, which stands for
*Employee Care and Opportunity*.

ECO Forms
---------------------------------------

All the ECO forms you'll need are stored on Nextcloud, in the *ECO* folder.

..  sidebar:: Why Paper?

    We are NOT a paperless company (primarily to save paper...we're not kidding.)
    You must print out, fill out, and sign any ECO form.

    If you don't have easy access to a scanner, you may use your smartphone
    to photograph the form. Take the effort to do this right! Ensure...

    * The form is straight,

    * The whole page is clearly visible,

    * The light is bright and even (no shadows or glare spots),

    * The surface behind the page is NOT visible.

    Alternatively, you may use software to sign by hand (such as using a
    graphics tablet). The point is to ensure the signature is indeed
    *your legal signature*.

All forms must be filled out, signed *by hand*, scanned in (see sidebar),
and emailed to ``eco@mousepawmedia.com``.

* **Formal Grievance**: If you are unable to resolve a conflict with a co-worker
  via informal discussions, you may file this form within 15 days of the
  most recent incident.

* **Promotion Request**: When you are ready to be promoted to Intern II,
  to graduate from the internship program, or otherwise be promoted to a
  higher seniority, you must fill out and submit this form. Interns will also
  need the appropriate **Internship Checklist**.

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