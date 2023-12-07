.. _gstaff:

Guide: Staff Introduction
#########################################

Welcome to MousePaw Media! We're really excited to have you aboard.
This tutorial will help you get started. If you need additional help, contact
your internship supervisor.

This tutorial will also get you started as a MousePaw Media staff member.
Read through it in order. Watch out for...

..  important:: These are things you should do now.

..  warning:: These are things you need to watch out for.

.. _gstaff_tech:

Technical Requirements
=======================================

We strongly recommend installing Ubuntu 22.04 or later on your computer.
If you're using Windows 10 or Windows 11, we recommend installing Ubuntu 22.04
via the Windows Subsystem for Linux.

We do support macOS if that's what you use, although we don't fully support it.

See :doc:`../setup_linux/install` for
instructions on installing Ubuntu 22.04. If you need help, contact your mentor.

.. _gstaff_security:

Security
=======================================

Your MousePaw Media account may grant you access to personal and private
company information, advanced controls that can potentially harm our systems,
and more. It is your responsibility to keep your MousePaw Media account secure!

.. _gstaff_security_password:

Passwords and Password Management
---------------------------------------

A good password should:

- Have a length of at least 16 characters; longer is always better.
- Have uppercase and lowercase letters.
- Have at least one number or symbol.

It is perfectly acceptable if you use real words, but you should avoid anything
that can be associated with you. Names, biographical data, pets, and signficant
dates should always be avoided! A random but memorable "passphrase" consisting
of random words is a good option, especially if you add symbols and numbers.\
For example, `Yodeling7_Goats_Twirl_Vicariously` is a strong password (but don't
use that exact passphrase, since it's written here!)

Bitwarden (described below) has an excellent password and passphrase generator.

To save your MousePaw Media credentials, you should use a secure password
manager. *Never* trust your web browser's "save password" feature for this.

If you aren't already using a secure password manager, install
`Bitwarden <https://bitwarden.com/>`_. Besides being free and open-source,
it is end-to-end encrypted, so your passwords can never be accessed in a data
breach of Bitwarden's servers.

1.  Go to https://bitwarden.com and tap :guilabel:`Get Started` to create
    a **free** account.

2.  Use your personal email address, and create a secure master password.
    Your password should be long, unique, and never consist of pet names,
    relative names, or other personal information.

3.  Write down your master password and store it in a secure physical location.
    NEVER keep it on a digital device. Be advised, for security reasons, if you
    lose your Bitwarden master password, you will NEVER be able to recover
    either it or any information you stored in your Bitwarden account.
    A password reset on Bitwarden will erase everything in your account.

4.  Go to https://bitwarden.com/download/ and install the Bitwarden extension
    for your web browser and each of your devices. This will allow you to
    access your passwords and other secure data stored in Bitwarden from any
    device, so long as you know your master password.

..  warning:: WE WILL NEVER ASK YOU FOR YOUR PASSWORD! Our IT department has
    the ability to reset any account password if necessary.

..  note:: Set up Bitwarden now and store your MousePaw Media credentials.
    If you already use a different secure password manager, add your MousePaw
    Media credentials to that instead.

.. _gstaff_security_mfa:

Multi-Factor Authentication
---------------------------------------

Passwords are a good first step in securing your account, but they're not the
whole story. If your password is compromised, **Multi-Factor Authentication**,
or **MFA**, is the next line of defence. MFA requires that you use a separate
physical device, like your smartphone, to verify that a login is indeed coming
from you, and not just some random person who has your username and password.

To set up MFA, you will need to install an authenticator app. Unless you
are already using another trustworthy authenticator app, please install one
of the following:

- To use your iOS or Android smartphone as your MFA device, install the
  Microsoft Authenticator app.

- To use your Linux machine as your MFA device, install the
  `Authenticator app <https://flathub.org/apps/details/com.belmoussaoui.Authenticator>`_
  from Flatpak.

..  note:: Set up an MFA authenticator app now.

In the sections that follow, you will be installing MFA for GitLab, which
handles authentication for most of the rest of our collaboration tools.

-*DO NOT** skip those sections!

.. _gstaff_security_tips:

Security Tips
---------------------------------------

All the security tools in the world mean nothing without a good dose of
common sense. Here are some very important things to keep in mind at all times.

..  warning: Read this section carefully!

1.  Never share your password with *anyone*. We won't ask for it. Period.

2.  Store your passwords in your secure Password Manager.

3.  Don't leave your work devices unlocked and unattended.

4.  Consider enabling full-disk encryption when you install Linux.

5.  Be careful clicking on unusual or disguised links in emails, even if
    the email looks like it's from within the company or from a coworker.
    Verify URLs and SSL certificates before entering your password or other
    information.

6.  If you lose a device you've used to access your MousePaw Media account, or
    which you use for MFA, contact us **immediately!**

.. _gstaff_security_recover:

Recovering Access
---------------------------------------

If you lose access to your MousePaw Media account at any point, whether
that's a missing password or a lost or non-functioning MFA device, don't panic!
MousePaw Media administrators are able to reset passwords and MFA credentials.

To start the process, contact ``it@mousepawmedia.com``. We will arrange to work
with you *over video chat* (for authentication purposes) to restore access
to your account.

.. _gstaff_network:

Staff Network
=======================================

The Staff Network consists of five major components:

- SOGo [Email/Calendar] (mail.mousepawmedia.com)
- GitLab (gitlab.mousepawmedia.com)
- Discourse (discourse.mousepawmedia.com)
- Nextcloud (cloud.mousepawmedia.com)
- OrangeHRM (eco.mousepawmedia.com)

We also use `Jitsi Meet <https://meet.jit.si/>`_ for video chat.

.. _gstaff_email:

Email
=======================================

As with any job, email and calendar are essential. You were issued a company
email address, which comes with a calendar and a contact book.

..  warning:: Be sure to check your company email every single day, and
    respond in a timely fashion!

.. _gstaff_email_sogo:

SOGo Setup
---------------------------------------

You can check your email and calendar through Nextcloud (recommended) or the
SOGo web client. This is useful if you don't want to configure an
external client, or need to check your email without access to your usual
device.

First, go to https://mail.mousepawmedia.com/SOGo. Sign in using your full
company email address and your password.

..  important:: Access your company email through the SOGo client.

.. _gstaff_email_settings:

Mail Settings
---------------------------------------

You can access a number of advanced settings for your email account by going
directly to `mail.mousepawmedia.com <https://mail.mousepawmedia.com>`_ and
logging in with your full email address and password.

Some things you can do from here include:

- Viewing login history (from :guilabel:`Mailbox -> General`)

- Adjust additional mailbox settings under :guilabel:`Mailbox -> Settings`.

- Setting up temporary email aliases, which are useful when you need to
  provide an email address for something, but want to be able to delete it
  if it starts to receive spam.

- Control spam settings, whitelist/blacklist email addresses.

- Generate app passwords for IMAP/SMTP/CalDAV/CardDAV logins without exposing
  your company email address.

- Check the intercepted spam in Quarantine, and take action on it (including
  delivering to mailbox) by clicking :guilabel:`Show item`,
  scrolling down, and clicking :guilabel:`Actions`.

You can also jump right to SOGo from here via the :guilabel:`Login to webmail`
button under :guilabel:`Mailbox`, or from the :guilabel:`Apps` menu at
upper-right.

.. _gstaff_email_imap:

IMAP/SMTP Access
---------------------------------------

You can also connect your account to an external email client, such as
Outlook, Apple Mail, Thunderbird, or your mobile device.

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

  - Port: ``587`` (recommended) or ``465``.

  - Connection security: ``STARTTLS`` (for post 587) or ``SSL/TLS`` (for port 465)

  - Authentication method: ``Normal password``

..  important:: Connect your company email to an email client, such as
    Thunderbird, Evolution, or your smartphone.

.. _gstaff_eco:

OrangeHRM
===========================================

Our "human resources" department is called **ECO**, which stands for
-Employee Care and Opportunity*.

Most ECO-related tasks are handled through OrangeHRM, including:

- Time Tracking
- Leave (time off)
- Performance Reviews
- Employee records

You are responsible for reporting your time **every day you work**, and you
must submit your timesheets every week.

.. _gstaff_eco_login:

Logging Into OrangeHRM
---------------------------------------

Your OrangeHRM account is handled separately from your GitLab account.
You will receieve an email with your initial password for OrangeHRM.
If you have not yet recieved this email, contact ``eco@mousepawmedia.com``
to request an account.

Go to ```https://eco.mousepawmedia.com`` and log in. Click your name in the
upper-right corner of OrangeHRM and click :guilabel:`Change Password`.
Change your password on the screen that appears. You may use the same
password as for GitLab if you prefer, or you may create a different password.
In either case, our password and security policies still apply! Once you've
entered your new password, click :guilabel:`Save`.

..  important:: Log into OrangeHRM.

.. _gstaff_eco_pim:

Updating Your Personal Information
---------------------------------------

For administrative and legal reasons, we keep track of the contact
information for our staff. This information is kept secure, and will not
be accessed or used outside of ECO and administration.

On the left, click :guilabel:`My Info`. Fill out the following fields.
Be sure to click :guilabel:`Save` on each page.

- Profile photo (click the gray silhouette): must be a head-and-shoulders photo of you.
- Personal Details
    - Employee Full Name
    - Nationality: based on current permanent residency, *not* heritage.
    - Marital Status
    - Date of Birth
    - Gender (you may omit if your gender is not present).
- Contact Details
    - Address
    - Telephone
        - Home/Mobile: shown only to ECO/admins
        - Work: shown in staff directory, include if you want others to be able to call.
    - Email
        - Work Email: shown in staff directory; use your MousePaw Media email.
        - Other Email: shown only to ECO/admin
    - Emergency Contacts
        - (Please add at least one emergency contact.)

We do *NOT* request that you provide your driver's license, dependents, or
immigration records.

..  important:: Go to :guilabel:`My Info` and fill out your information.

.. _gstaff_eco_time:

Logging Your Time
---------------------------------------

You **must** report all time worked at MousePaw Media by Monday the following
week. This serves two functions:

- Indicates your attendance, along with your journal (described later).
- Creates accountability for your use of volunteer time.

..  warning:: Reporting your time is of utmost importance! Set reminders for
    yourself to remember to fill out and submit your timesheets. Repeated
    failures to do so may result in disciplinary action.

To log your time, in OrangeHRM, click :guilabel:`Time` on the left. You
should be on the :guilabel:`My Timesheets` page by default, but if not,
go to :guilabel:`Timesheets` and :guilabel:`My Timesheets`.

Ensure you are looking at the desired timesheet period, and click
:guilabel:`Edit`. Each major project you work on will have its own row
on the timesheet. You may add as many rows as you need by clicking
:guilabel:`Add Row`.

Under :guilabel:`Project`, type and select the name of the project you're
working on. Aside from our actual projects, such as :code:`Platform - IOSqueak`
or :code:`Applications - Quarkboard`, we have several other entries:

- General - Community: interacting with or moderating our community.
- General - Meetings: syncronous meetings.
- General - Mentorship: all activities relating to training interns.
- General - Other: anything that doesn't fit in another category. USE SPARINGLY.
- General - Setup: setting up workstation, tools, development environment, etc.
- General - Training: using MousePaw Media curated training materials. Do NOT use for "research".
- DevOps - DevOps: maintaining build, test, and deployment systems (regardless of project).
- DevOps - Documentation: writing documentation that is NOT specific to a project.
- DevOps - Standards: maintaining standards.

We also have administration-related "projects". Non-management staff should
NOT use these without first confirming with their supervisor.

- Administration - Administration: management activities.
- Administration - ECO: ECO (HR) management activities.
- Administration - Hiring: screening, interviewing, or reviewing candidates.
- Administration - IT: maintaining our servers or collaborative platforms.

In all cases, select :guilabel:`General` under the :guilabel:`Activity` section.

Enter the number of hours for each day. Use decimal hours to represent
partial hours, such as :code:`1.5` for an hour and a half.

When you've finished updating your timesheet for the day, click :guilabel:`Save`.

..  note:: It is best to update your timesheets at the end of each day you work,
    rather than trying to remember your hours at the end of the week.

When you have submitted all of your time for the week, click :guilabel:`Submit`
to submit your timesheet.

..  warning:: You **MUST** submit your time for each week by the following
    Monday. Set a reminder for yourself!

..  important:: Set a reminder for yourself to log your time.

If you need a tool to help you track your time, check out the
`Timecard <https://codemouse92.github.io/Timecard/>`_ application, created by
Jason C. McDonald.

.. _gstaff_eco_leave:

Requesting Leave
---------------------------------------

When you are going to miss a week, or come in below your required number of
hours for a week, you should submit for Leave.

..  warning:: Even if you do not have a mandatory hours requirement, we
    strongly recommend requesting leave when you are going to be out of
    the office for a week or more! This ensures that everyone knows you will
    be unavailable, and when you will be back.

To apply for leave or manage your leave entitlements, go to :guilabel:`Leave`.

:guilabel:`Entitlements` -> :guilabel:`My Entitlements` shows how much
of each type of leave you have available. Due to our low hour requirements,
each "Day" of leave actually entitles you to a full **week**. If you only plan
to be out for part of the week, you can submit for a partial day.

:guilabel:`Apply` allows you to apply for leave. Select the Leave Type you
want to apply. Set the :guilabel:`From Date` to Monday of the first week you
want to take leave for. Set the :guilabel:`To Date` to the Monday of the
last week you want to take leave for, even if it's the same as
:guilabel:`From Date`.

Normally, you can set :guilabel:`Duration` to :code:`Full Day` to just take
the whole week(s). However, if you have a mandatory weekly hour commitment
and want to work less than those hours, you can select :code:`Specify Time`.
The times you enter *DO NOT* matter, only the actual duration.

Describe the nature of your leave in :guilabel:`Comments`, and then select
:guilabel:`Apply`.

:guilabel:`My Leave` shows your submitted leave requests and their status.
You can also click :guilabel:`Cancel` next to a leave request if you no longer
wish to take that leave.

..  note:: If you have further questions about leave, contact the ECO
    department at ``eco@mousepawmedia.com``.

ECO Forms
---------------------------------------

Some additional ECO activites are handled through Discourse (discussed later).

- **Formal Grievance**: If you are unable to resolve a conflict with a co-worker
  via informal discussions, you may file this form within 15 days of the
  most recent incident.

- **Promotion Request**: When you are ready to be graduate from the internship
  program, or otherwise be promoted to a higher seniority, you must fill out
  and submit this form. Interns will also need the appropriate
  -*Internship Checklist**.

- **Resignation Request**: If you choose to leave MousePaw Media, you must
  file a resignation request. If you're an intern, we may choose to terminate
  your staff role with us instead of accepting the resignation, as specified
  in your contract.

Management Forms
--------------------------------------------

There are a couple more ECO forms which are accessible only to management.

- **Disciplinary Warning Notice**: For serious and/or recurring
  problems, a supervisor may detail the incident and the expected remedy
  using this form. If you receive one, be sure to read it, sign it, and
  send it back via e-mail ASAP.

- **Termination**: In the rare and unfortunate case where an staff member
  must be fired, we use this form. There is also a separate
  -*Internship Termination** form.

.. _gstaff_gitlab:

GitLab
===========================================

We run our own private instance of GitLab at ``https://gitlab.mousepawmedia.com``.
This is where most of our development work takes place. Most importantly, it
hosts our repositories.

In addition, your MousePaw Media GitLab account serves as the single-sign on
authentication for most of the rest of the network.

.. _gstaff_gitlab_setup:

Setting Up Your Account
-------------------------------------------

New Account
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If you did not previously have a MousePaw Media GitLab account, you will
be issued one associated with your MousePaw Media email address. Look for the
email in that inbox with the link to log in. After agreeing to terms,
your account will be active and ready for use.

Go to your User Settings by clicking your picture in the sidebar, and clicking
:guilabel:`Edit Profile`. On the left, click :guilabel:`Password`, and change
your password.

Now click :guilabel:`Account` and add two-factor authentication.

..  important:: If you have a new account, set it up now following the
    instructions above.

Existing Account
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If you already have a MousePaw Media GitLab account, you can simply associate
your new MousePaw Media email address with it. Go to your User Settings by
clicking your picture in the sidebar, and clicking :guilabel:`Edit Profile`.
Then select :guilabel:`Emails`. Click :guilabel:`Add new email`, enter your
MousePaw Media email address, and click :guilabel:`Add email address`.
You will be prompted to verify the address. Once you have, you should set your
MousePaw Media email address as your Primary email for the account.

You will also (probably) want to change your username to your MousePaw Media
username. You can do this form :guilabel:`Edit Profile` and :guilabel:`Account`.
Enter your MousePaw Media username under :guilabel:`Change username`, and click
:guilabel:`Update username`.

..  note::: The unintended side effects mentioned are related to any private
    repositories you have on our GitLab instance, which will be redirected to
    new paths. This is unlikely to affect you, as we don't allow non-staff to
    create private repositories.

Finally, if you haven't already added two-factor authentication, click
:guilabel:`Account` and add two-factor authentication.

..  important:: If you have a pre-existing account, set it up now following the
    instructions above.

.. _gstaff_gitlab_profile:

Editing Your Profile and Settings
-----------------------------------

Go to your User Settings by clicking your picture in the sidebar, and clicking
:guilabel:`Edit Profile`. Fill out the following fields at a minimum, as well
as any others you wish:

- Public avatar (a head-and-shoulders photo of you)
- Time settings
  - Time zone
- Main settings
  - Full name
  - Pronouns
  - Public email (set to your MousePaw Media email)
  - Job title
  - Organization (MousePaw Media)

Click :guilabel:`Update profile settings`.

Now click :guilabel:`Preferences` on the left sidebar. Here, you may define
the appearance and behavior of GitLab to match your preferences.

..  important:: Fill out your profile.

.. _gstaff_gitlab_groups:

Groups
-----------------------------------

On the main sidebar for GitLab, click :guilabel:`Groups`. This lists all of
the groups on our GitLab instance which you have access to view.

Groups serve two purposes in our GitLab:

1.  Groups organize projects by department: Platform, Applications, DevOps,
    and Content Development. There is also a Web project under Applications.
2.  Groups manage permissions throughout our network. In particular, the
    Staff group indiciates a person is official MousePaw Media staff, and
    grants access to staff-related resources.

.. _gstaff_gitlab_projects:

Projects
-----------------------------------

On the main sidebar for GitLab, click :guilabel:`Projects`. Here, you will
see all of the projects at MousePaw Media you have access to, as well as their
associated Group (department). Click on one to navigate to the repository.

On the project page, you'll notice that the sidebar has a lot of entries.
Here's a brief tour of the most important:

- Plan

  - Wiki: This is where we store *internal* documentation for the project,
    which is written for the reference of developers working on the code.
    External documentation is in the repository itself, usually in the
    ``docs/`` directory.

- Code

  - Merge requests: Proposed changes to the code. All work you submit to
    a repository must be in a merge request.
  - Repository: the source code itself.
  - Branches: a list of all branches in the repository.
  - Commits: a history of changes to the code.
  - Tags: named points in the repository history which are important.
  - Snippets: small pieces of code and text associated with the project, but
    which we don't want to include in the main repository at present. Think
    of this as a project-specific "paste bin".

- Build

  - Pipelines: chains of test and build jobs that are running, completed, or
    failed.
  - Jobs: individual test or build jobs that are running, completed, or failed.
    These are run as part of pipelines, but can be browsed individually too.
  - Artifacts: built versions of the project, produced by certain jobs.

- Deploy

  - Releases: sets of artifacts and changelogs that constitute a single
    released version of the project. Associated with a specific tag.
  - Package Registry: a registry of packaged artifacts associated with the
    project. Users with access permissions can download/install packages
    directly from this registry.
  - Container Registry: a registry of Docker images associated with the
    project. Users with access permissions can download Docker images directly
    from this registry.

There are a number of other GitLab features. Some are more advanced, and others
we simply do not use.

..  note:: We do NOT use GitLab Issues, as it is simply too limited to be
    useful. We are in the process of building our own issue tracking system,
    but in the meantime, we use Discourse (covered below).

.. _gstaff_gitlab_snippets:

Snippets
-----------------------------------

If you want to share a piece of code or text which is *not* strictly associated
with a project, you can use the Snippets tool. From the main sidebar of GitLab,
select :guilabel:`Snippets`. Then, in the upper-right, click
:guilabel:`New snippet.`

If your snippet is meant to be associated with a particular project, navigate
to that project in GitLab, and go to :guilabel:`Code` -> :guilabel:`Snippets`
instead.

.. _gstaff_gitlab_learn:

Learn More about GitLab
-----------------------------------

GitLab has very thorough and up-to-date documentation:
`GitLab Docs <https://docs.gitlab.com/>`_. You can access this, as well
as see the current version of GitLab we are running, by clicking
:guilabel:`Help` in the lower-left corner of GitLab anytime.

..  note:: We are running the Free Self-Hosted tier of GitLab EE, so some
    features are not available to us. In the documentation, take note when
    a feature is indicated as being for the Premium or Ultimate tier or
    SaaS offering.

.. _gstaff_discourse:

Discourse
===========================================

Most of our collaboration takes place on Discourse. It is home to our:

- Community
- Curated training material
- Q&A
- Issue Tracker
- Software Product Specifications
- Forms and Surveys

You can sign into Discourse at ``https://discourse.mousepawmedia.com``
using your MousePaw Media GitLab account.

Be sure to read the community rules by clicking the :guilabel:`FAQ` button
from the left sidebar.

..  important:: Read the FAQ!

.. _gstaff_discourse_basics:

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

.. _gstaff_discourse_journal:

Journal
-----------------------------------

Every member of the staff is **required** to maintain a weekly journal,
detailing what they worked on in the past week. In addition to posting
one's own journal, each staff member must reply to two other Journal entries.

..  warning:: Posting your Journal entry, and replying to two other Journal
    entires, is a mandatory part of attendance!

You can view the journals by visiting the "Journals" category,
or by clicking :guilabel:`Journals` on the left sidebar. Only members of
staff are allowed to view this category.

..  note:: If you cannot view the category, ask an administrator to grant
    you "moderator" privileges, which designates you as a staff member.

To post your own journal entry, click :guilabel:`New Topic` in the Journals
category, and use the provided template.

..  important:: Set a reminder to post your journal entry by Monday each week,
    and reply to at least two other staff member journal entries.

.. _gstaff_discourse_issues:

Issue Tracker
-----------------------------------

We are in the process of building our own issue tracker. In the meantime,
Discourse is serving as our issue tracker, and will continue to be the
staging area for community-reported issues.

You can view the issue tracker by visiting the "Issue Tracker" category,
or by clicking :guilabel:`Issue Tracker` on the left sidebar.

.. _gstaff_nextcloud:

Nextcloud
===========================================

Nextcloud is our staff-only file share. It not only hosts our design and
administration files, but also a number of shared resources. If you work
in the Design or Content Development departments, you'll upload a lot of
your work to Nextcloud.

.. _gstaff_nextcloud_settings:

Settings
-------------------------------------------

When you first log into Nextcloud, click your profile photo in the upper-right
corner and select :guilabel:`Personal settings`. This will take you to your
profile and settings screen.

Click :guilabel:`Notifications` at left. Under :guilabel:`Activity`, change
what notifications you wish you receive. In particular, you will probably
want to uncheck most of the :guilabel:`Email` notifications to keep your
inbox from getting overwhelming.

..  sidebar:: Authorizing Discourse and Nextcloud to communicate requires an
    additional step, which can get tricky in some browsers.

    On Chrome, click the tiny icon on the right of the address bar that, when
    hovered over, says :guilabel:`page wants to install a service handler`.
    Click this and click :guilabel:`Allow` and :guilabel:`Done`. If you click
    the wrong button or this icon disappears (or is missing) before you can
    allow, go to :code:`chrome://settings/handlers`, find the entry for
    :code:`cloud.mousepawmedia.com`, and remove it. Ensure you set
    :guilabel:`Sites can ask to handle protocols`, and then return to Nextcloud,
    refresh, and try again.

    On Firefox, at the top of your browser, you should see a prompt to
    authorize Nextcloud to handle :code:`web+nextclouddiscourse` links.
    Click the :guilabel:`Add application` button. If clicking "Authorize" on
    Discourse does nothing or opens a different application, go to your browser
    settings. If this happens, go to Firefox Settings. Under
    :guilabel:`General`, scroll down to :guilabel:`Applications`, find the
    entry for :code:`web+nextclouddiscourse`,
    and set the action to :guilabel:`Use cloud.mousepawmedia.com`.

Click :guilabel:`Connected Accounts` at left.

Under :guilabel:`Discourse integration`, ensure the Discourse instance
address is set to :code:`https://discourse.mousepawmedia.com`. Ensure you
authorize Nextcloud to handle "web+nextclouddiscourse" links (see sidebar) and
click :guilabel:`Connect to Discourse`. You will be redirected to Discourse,
where you should click :guilabel:`Authorize`.

Under :guilabel:`GitLab integration`, ensure the
:guilabel:`GitLab instance address` field is set to
:code:`https://gitlab.mousepawmedia.com`. Click :guilabel:`Connect to GitLab`

.. _gstaff_nextcloud_mail:

Mail
------------------------------------------

Nextcloud has an excellent Mail application which we strongly recommend using
for webmail access, instead of SOGo. You **should** set this up!

On the top bar of Nextcloud, click the icon for :guilabel:`Mail`. You will see
the :guilabel:`Connect your mail account` page, which will be automatically
filled in with your name and MousePaw Media email address. Enter your email
password and click :guilabel:`Connect`.

Once your inbox loads, click the three dots next to your email address at left
and select :guilabel:`Account settings`. Scroll down to :guilabel:`Signature`,
and fill in your signature. We recommend this emplate:

..  code-block:: text

    Your Name Here
    Position
    MousePaw Media

    Visit Us Online: MousePawMedia.com

Press :guilabel:`Save signature` to save.

Under :guilabel:`Writing mode`, select :guilabel:`Rich text`.

.. _gstaff_nextcloud_dashboard:

Dashboard
-------------------------------------------

When you first visit Nextcloud, you will see the Dashboard, which provides
notifications and links for the entire staff network.

By default, you'll see the following boxes:

- Welcome: an initial message providing a link to this documentation.
- Announcements: important news from MousePaw Media administration.
- Discourse notifications: notifications from our Discourse instance.
- Unread mail: new messages from your email.
- Upcoming cards: tasks with approaching deadlines from Nextcloud Deck.
- GitLab To-Dos: pending code reviews and other tasks from GitLab.
- Pending approvals: anything in Nextcloud awaiting your review.
- Staff Portal: links to the rest of the staff network (also on the top bar).

Scroll to the bottom and click :guilabel:`Customize` to edit this Dashboard
if you desire.

..  note:: If the weather is displaying in Celsius, and you want Fahrenheit,
    click your picture and select :guilabel:`Personal settings`. Change
    :guilabel:`Locale` to anything else, and then back to your locale, and
    the weather will display in the temperature scale used by your locale.
    (This is a bug in Nextcloud.)

.. _gstaff_nextcloud_deck:

Deck
-------------------------------------------

The Deck application on Nextcloud is a task tracker. You can use it for just
yourself, or you can share it with any other staff members at MousePaw Media.

Select a board under :guilabel:`All boards`, or create a new board with
:guilabel:`Add board`. To share the board, click the three dots next to its
name and click :guilabel:`Board details`; to rename, click
:guilabel:`Edit board`.

You can customize the columns, and add, edit, and remove cards. To display
the card on the :guilabel:`Upcoming cards` or the Dashboard, be sure to set a
due date on the card. To do this, click the three dots on the card and select
:guilabel:`Card details`.

.. _gstaff_nextcloud_calendar:

Calendar
-------------------------------------------

The Nextcloud calendar enables easy scheduling with colleagues. To use this
application, click the icon for :guilabel:`Calendar` on the top bar of
Nextcloud.

To schedule a new event, click the :guilabel:`+ Event` button at left. If you
want to invite other people, click :guilabel:`More`. Fill out all the fields
you want. Click :guilabel:`Attendees` and type and click the names of staff
members you want to invite, or email addresses if they're not staff members.
Click :guilabel:`Show busy times` to see everyone's availability, to aid with
finding good times for scheduling.

.. _gstaff_nextcloud_calendar_external:

Importing External Calendars
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

To make scheduling easier, you should subscribe to any external calendars
you routinely use for scheduling. This may include a personal calendar,
a work calendar, or a school calendar.

1.  Get the **public** CalDAV/WebDAV/ICS URL of the external calendar you
    want to add. If you're concerned about privacy, at least enable a public
    link that shows free/busy status without event details. (If you need
    help with this, contact your mentor.)

2.  In Nextcloud Calendar, click :guilabel:`New Calendar` and
    :guilabel:`New subscription from link (read-only)`. Paste the link from
    step one and click the arrow.

3.  Hover over the calendar name in the list at left and click the pencil
    icon to edit the name and color.

4.  Click the color icon next to the calendar name to toggle its visibility.
    Others will still see your free/busy status when scheduling with you.

..  important:: Import your web calendars now.

.. _gstaff_nextcloud_calendar_hours:

Setting Office Hours
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

You may want to define office hours for yourself, which indicate when you
are generally available for meetings. To do this, click your profile picture
in the upper-right and click :guilabel:`Personal settings`. On the left,
select :guilabel:`Availability`.

On that page, set your time zone, and add your working hours. Remember that
this is only a general availability. Your calendar can be used to indicate
specific times when you're unavailable.

Also, check the box that says
:guilabel:`Automatically set user status to "Do not disturb"...`
to make it easier for colleages to determine whether you're available or not.

..  important:: Set up your general availability.

.. _gstaff_nextcloud_calendar_caldav:

Accessing via Calendar Client
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

If you use an external calendar application (and you should!), you can
work directly with your MousePaw Media calendar via CalDAV or WebDAV.

If your calendar application supports CalDAV with authentication, use
your private CalDAV link. In the Calendar appl, next to the calendar you
want to access externally, click the pencil icon to edit the calendar.
Next, click the clipboard icon to the right of :guilabel:`Internal link`.
You will add this to your calendar application as a CalDAV source. You'll
next need to create an application password by clicking your profile photo,
clicking :guilabel:`Personal settings`, and :guilabel:`Security`. At the bottom
of that page, enter the name of your calendar application (for your personal
reference) and click :guilabel:`Create new app password`. Use the username
and password provided there to authenticate for your CalDAV calendar in your
calendar application.

If your calendar application does *not* support authentication, use WebDAV
instead. In the Calendar appl, next to the calendar you want to access
externally, click the pencil icon to edit the calendar. Click the :guilabel:`+`
button next to :guilabel:`Share link`. Click the three dots to the right
of that row and click :guilabel:`Copy subscription link`. Use that link to
subscribe to your public WebDAV calendar.

..  important:: Add your calendar to any external calendar apps you use.
    You can also susbcribe to your calendar in services like Outlook or
    Google Calendar.

.. _gstaff_nextcloud_talk:

Talk
-------------------------------------------

For convenience, we've enabled the Talk application in Nextcloud, which
allows you to create text-based chatrooms. This will eventually be integrated
into our Mattermost instance (coming soon), or else disabled.

.. _gstaff_nextcloud_client:

Nextcloud Client
-------------------------------------------

If you'll be using Nextcloud regularly, you may consider setting up the
Nextcloud Client on your computer.
See `Download and install Nextcloud <https://nextcloud.com/install/>`_.

.. _gstaff_nextcloud_tasks:

Regular Nextcloud Tasks
-------------------------------------------

- Read and response to emails in a timely fashion in Mail.
- Schedule and RSVP for meetings in Calendar.
- Manage your personal tasks in Deck.
- Access shared files, including design assets.
- Upload work. (Content Development, Design)
- Review and proofread files. (Content Development, Design)
- Access your library loans.

When you upload files meant to be shared, be sure to place them in the
appropriate shared folder.

.. _gstaff_policies:

Employee Handbook
=======================================

Our Employee Handbook outlines all the company policies, rules, and
expectations for staff at MousePaw Media. You should be familiar with
the latest version of these policies at all times.

We publish the latest version of the Employee Handbook on our Nextcloud, under
the ``ECO`` directory.

..  important:: Download the latest copy of the Employee Handbook from
    that link, and save it to your work computer for quick reference.
    Read it through now.

You will be emailed whenever a new version of the Employee Handbook is
published.

Next Steps
===========================================

Previous MousePaw Media graduates have written up some tips for new interns!
You can read those on Discourse at the link below:

..  important:: Read `Tips for Interns <https://discourse.mousepawmedia.com/t/tips-for-interns/90>`_

You can learn more about the different parts of the Staff Network in the
other sections of this documentation.

Programmers should check out these sections next:

- :ref:`genv`
- :ref:`grevision`
- :ref:`gbuild`

Content Developers should check out this section next:

- :ref:`genv_content`

Design+Production and Mass Communication staff should check out this
section next:

- :ref:`genv_designprod`
