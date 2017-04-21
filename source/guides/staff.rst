.. _gstaff:

Guide: Staff Introduction
#########################################

Welcome to the MousePaw Media family! We're really excited to have you aboard.
This tutorial will help you get started. If you need additional help, contact
your internship supervisor.

This tutorial will also get you started as a MousePaw Media employee.
Read through it in order. Watch out for...

..  IMPORTANT:: These are things you should do now.

..  WARNING:: These are things you need to watch out for.

.. _gstaff_policies:

Company Policies and Expectations
=======================================

Our Company Policies outline all the rules and expectations for staff at
MousePaw Media. You should be familiar with the latest version of these
policies at all times.

We maintain the latest copy of the `Company Policies on Phriction <https://phabricator.mousepawmedia.net/w/standards/policy/>`_.

..  NOTE:: Read the latest version of the Company Policies on the link above.
    You should use your company LDAP credentials to login to Phabricator.
    Then, Subscribe to the page via :menuselection:`Actions -> Subscribe`.


.. _gstaff_tech:

Technical Requirements
=======================================

You **must** have Debian Linux, ideally an Ubuntu-based distro (version 16.04
or later), installed directly on your computer. See :doc:`/setup_linux/install`.
If you need help, contact Jason C. McDonald.

.. _gstaff_network:

Staff Network
=======================================

The Staff Network consists of six components:

* Webmail (mousepawgames.com/webmail)

* eHour (ehour.mousepawmedia.net)

* Phabricator (phabricator.mousepawmedia.net)

* NextCloud (nextcloud.mousepawmedia.net)

* Jenkins (jenkins.mousepawmedia.net)

We also use IRC and Jitsi for regular communication.

.. _gstaff_network_webmail:

Webmail
------------------------------------------

..  NOTE:: Webmail is hosted on our Provo, Utah servers, and thus is
    available 24/7!

Email is one of our primary ways of communicating at MousePaw Media. You have
a company email address issued to you.

..  WARNING:: Be sure to check your company email every single day, and
    respond in a timely fashion!

You can check your email through any of the three build-in web clients. If you
need help choosing a webmail client, check out this table...

+----------------+--------------+------------------------------------------------------+
| If you like... | Try          | Because...                                           |
+================+==============+======================================================+
| Outlook.com    | Horde        | Integrated calendar, tasks, and notes.               |
+----------------+--------------+------------------------------------------------------+
| Gmail, Yahoo   | RoundCube    | Simple graphical interface, less bells and whistles. |
+----------------+--------------+------------------------------------------------------+
| The 1990s      | SquirrelMail | Text-based interface.                                |
+----------------+--------------+------------------------------------------------------+

You can also connect your account to an external email client, such as Thirdbird
or your mobile device. If your email client cannot automatically detect the
necessary settings, follow these instructions...

1.  Log into Webmail with your full company email address and your password.

2.  On the lower list of links, click :guilabel:`Configure Mail Client`.

3.  Scroll down and write down the information from the box titled
    :guilabel:`Secure SSL/TLS Settings`. Use these to set up the email
    client of your chocie. For desktops and laptops, we recommend Thunderbird.

..  IMPORTANT:: Access your company email using one of the methods above.

Regular Webmail Tasks
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* Correspond with co-workers. Read and respond to messages in a timely fashion.

* Send your check-in email on the same day each week, detailing what you've
  done over the past week and what you're planning to do over the upcoming week.
  Including the Phabricator Maniphest task IDs/links for the tasks you've been
  working on. This is also a good time to ask your supervisors questions.

..  IMPORTANT:: Log into your webmail and send an email to your supervisors -
    specifically your internship supervisor and team lead. Let them know what
    day you would like your weekly check-in email to be on.

..  WARNING:: Your check-in email is how we track your job attendance. Be sure
    to send it on your regular day **every single week**!

.. _gstaff_network_ehour:

eHour
-----------------------------------

..  NOTE:: eHour is on our development server, meaning it is subject to the
    Hawksnest server hours.

eHour is where we track time. You are responsible for reporting your time
**every day**. Each day's time sheets are locked the next morning at 10am.

..  WARNING:: If you forget to log your time, you will not be reminded.
    Contact your supervisor if you missed the deadline.

To log your time, go to eHour. Login using your company username and password.
You will be on the :guilabel:`Your Hours` section by default, with the current
week highlighted.

Enter your time (in hours) on the appropriate box for each day. You should also
want to include notes on each day (by clicking the pencil), listing the
Phabricator objects you worked on (Tasks, Revisions, wiki pages, etc.)

..  WARNING:: These notes are not a replacement for your check-in email.

Finally, click :guilabel:`Store` in the lower-right corner to store the hours.

Regular eHour Tasks
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

* Log your hours *every day you work*.

* Seriously, that's it. **Log your time!**

.. _gstaff_network_phab:

Phabricator
--------------------------------------

..  NOTE:: Phabricator is on our development server, meaning it is subject to
    the Hawksnest server hours.

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

..  NOTE:: Please fill out at least five "Fun Stuff" fields, as this is what
    we will use to craft your profile on the MousePaw Media website.

5.  Click :guilabel:`Save Profile` at the bottom.

6.  Click :guilabel:`Edit Settings` at right. Alternatively, click your icon
    picture at the top of the page and select :guilabel:`Settings`.

7.  Select :guilabel:`Account` at left, and set :guilabel:`Pronoun`
    appropriately. Click :guilabel:`Save Changes`.

8.  Click :guilabel:`Conpherence Preferences` at left, and set it to
    "Send Notifications". (Otherwise, you may get spammed.)
    Click :guilabel:`Save Changes`.

9.  Click :guilabel:`Desktop Notifications` at left. Select the option
    ``Send Desktop Notifications Too``, and save. Then, click
    :guilabel:`Enable Desktop Notifications`. You may consider clicking
    :guilabel:`Send Test Notification` in the upper-right corner to test.
    Then, click :guilabel:`Save Preference`.

10. Click :guilabel:`External Accounts` at left. Add your GitHub account.
    Click :guilabel:`Save Changes`.

..  IMPORTANT:: You should also add your company email address to your GitHub
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

11. Click :guilabel:`Email Delivery` at left. DISABLE :guilabel:`Self Actions`,
    but leave :guilabel:`Email Notifications` ENABLED.
    Click :guilabel:`Save Changes`.

12. Click :guilabel:`Email Preferences` at left. Here, you may shut off many
    email notifications by selecting the :guilabel:`Notify` option for any
    given item.

..  WARNING:: Do NOT select "Ignore" for any notifications! All notifications
    are ultimately controlled by whether you're "Subscribed" to an object.

13. Take a few minutes to go through the rest of the settings independently.
    Use the menu at left to see more settings.

14. Click :guilabel:`Phabricator` in the upper-left corner to return to the
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

    Unless the goal will be completed in the next five minutes, **MAKE A TASK.**

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
    up. To install the client for Linux, see :ref:`nextcloud_client`.

..  IMPORTANT:: Remember, Windows is banned from the company for all
    staff-related tasks. This includes accessing Nextcloud, even through
    the Windows client.

3.  The Activity section allows you to customize notifications. You should leave
    most Stream options checked, so you'll know when things happen on Nextcloud.
    However, you may want to uncheck some Mail options, to keep email to a
    minimum.

4.  Uncheck the boxes labeled :guilabel:`List your own file actions in the stream`
    and :guilabel:`Notify about your own actions via email`, so you don't
    recieve notifications about your *own* actions.

If you'll be using Nextcloud regularly, you may consider setting up the
Nextcloud Client on your computer. See :ref:`nextcloud_client`.

Regular Nextcloud Tasks
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Nextcloud is where we store all important staff documents (see :ref:`gstaff_eco`),
and where we share a lot of common non-code files.

If you work in the Content Development department, you'll especially be
spending a lot of time on Nextcloud.

* Upload files.

* Review and proofread files.

* Collaborate on documents.

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

All forms must be filled out and signed *by hand*, scanned in, and emailed to
the relevant supervisors.

* **Formal Grievance**: If you are unable to resolve a conflict with a co-worker
  via informal discussions, you may file this form within 15 days of the
  most recent incident.

* **Internship Graduation Request**: When you are ready to graduate from the
  internship program, you must fill out this form and submit it to your
  internship supervisor. You will also need the appropriate
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

* **Employee Discipinary Warning Notice**: For serious and/or recurring
  problems, a supervisor may detail the incident and the expected remedy
  using this form. If you receive one, be sure to read it, initial and sign
  it, and send it back via e-mail ASAP.

* **Employee Termination**: In the rare and unfortunate case where an employee
  must be fired, we use this form. There is also a separate
  **Internship Termination** form.

Next Steps
===========================================

You can learn more about the different parts of the Staff Network in the
other sections of this documentation.

If you're an intern, you can find a list of all your assignments on
the appropriate Internship Checklist at the bottom of the
`Assignments Phriction page <https://phabricator.mousepawmedia.net/w/assignments/>`_.

Programmers should check out these sections next:

* :ref:`gcontrib`
* :ref:`gbuild`
