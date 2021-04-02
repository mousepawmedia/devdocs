..  _admin_accounts:

Account Management
##########################################

..  NOTE:: Section coming soon.

..  _admin_accounts_new:

Employee Account Creation
===========================================

All company accounts are managed (primarily) from our LDAP server, with
two notable exceptions.

* Postfix manages email accounts, so we can create additional addresses.

* LimeSurvey is on the remote server (mousepawmedia.com), without access to
  the LDAP.

The following instructions will create a new staff account.

..  _admin_accounts_new_generate:

Generating Login
-------------------------------------------

Before we begin, we must first create the username and password. Usernames
are composed of first and middle intitials and last name.

Passwords should be ranked as strong on both
`GRC Haystack <https://www.grc.com/haystack.htm>`_ and
`How Secure Is My Password <https://howsecureismypassword.net/>`_.

The username and password should be stored in the GPG-encrypted password
records (see :ref:`admin_gpg`).

..  _admin_accounts_new_email:

Creating Email Account
-------------------------------------------

Before we can access Postfix Admin, we must first activate the server controls
on Webster (our remote server). Any company IT with SSH root access can
do so by running...

..  code-block:: bash

    $ controls on

Now we can access Postfix Admin at
`mousepawmedia.com/postfixadmin <https://mousepawmedia.com/postfixadmin/login.php>`_
and log in with our company account. (Only administrators have access here.)

Select :menuselection:`Virtual List --> Add Mailbox`. Fill out the following:

* :guilabel:`Username`: The username for the user. Select the
  :code:`mousepawmedia.com` domain name from the drop-down menu below this
  field.

* :guilabel:`Password`: Enter the password for the user, and confirm it in
  :guilabel:`Password (again)`.

* :guilabel:`Name`: The first name, middle initial, and last name of the user.
  For example, "Jason C. McDonald".

* :guilabel:`Quota`: For all user accounts, this should be 100MB.

* :guilabel:`Active`: Ensure this box is *checked*, or else the new account
  will not work.

* :guilabel:`Send Welcome mail`: A message detailing first steps for new staff
  members was pre-programmed into Postfix. Check this box to automatically send
  it.

After you've filled out all these fields, click :guilabel:`Add Mailbox`.

In a new tab, make sure you can log into webmail at
`webmail.mousepawmedia.com <https://webmail.mousepawmedia.com/src/login.php>`_
using the new email address and password.

Once you've confirmed the account was created correctly, select
:guilabel:`Logout` from the top menu on Postfix Admin.

Be sure to have IT disable the control panel via...

..  code-block:: bash

    $ controls off

..  _admin_accounts_new_ldap:

Creating PawID (LDAP Account)
-------------------------------------------

To create a new LDAP account, go to the LDAP control panel on Hawksnest:
`LDAP Account Manager <https://mousepawmedia.net/lam/>`_. Login using the
LDAP management password.

On the :guilabel:`Users` tab, select :guilabel:`New user`:

Toward the upper section, set :guilabel:`RDN identifier` to `uid`.

..  NOTE: If you get this wrong when you first create the user, go to
    :guilabel:`Tree view` and rename the item manually from ``cn=whatever,``
    to ``uid=whatever,``

On the :guilabel:`Personal` tab, fill out the following fields at minimum:

* :guilabel:`First name`
* :guilabel:`Last name`
* :guilabel:`Email address`

On the :guilabel:`Unix` tab, fill out the following fields, leaving the others
as their defaults (or blank):

* :guilabel:`User name`: The company username.
* :guilabel:`Common name`: The full name - first name, middle initial, last name.
* :guilabel:`Primary group`: ``user``

Click ``Edit groups`` and add the appropriate groups. At minimum, be sure
to add either ``staff`` or ``community`` and the department name. Then click
``Back``.

Verify all the details entered, and then click :guilabel:`Save` at the top, and
then click :guilabel:`Edit again`.

Select :guilabel:`Set password` toward the top of the page. Set the password.

Click :guilabel:`Save` at the top.

The account is immediately active.


..  _admin_accounts_new_phab:

Creating Phabricator Account
-------------------------------------------

It is now possible to log into Phabricator using LDAP, but we want to do some
additional setup on that profile.

In a new private window (so you don't have to sign out of Phabricator yourself),
log into Phabricator using the new account.

The window that appears should display that you're Creating a New Account
using an external LDAP account. The username and real name should automatically
be pulled from the LDAP database.

Click :guilabel:`Register Account`. Once the Community Rules displays,
**immediately close out of the window**!

..  IMPORTANT:: You should **NEVER** accept the Community Rules on behalf of
    the new user!

..  _admin_accounts_new_phabaccess:

Managing Phabricator Permissions
-------------------------------------------

It isn't enough just to *create* a user account on Phabricator, as users have
very limited permissions by default (which is good for outside contributors).
We must manually specify this new person as a member of *staff*, and determine
what they have access to.

As you may know, this is handled through **Projects** on Phabricator. You
can add a user to a group by going to the Project, selecting
:guilabel:`Members` on the left, and clicking :guilabel:`Add Members`.

..  NOTE:: If you aren't allowed to add a member to a project, check whether
    the project has *subprojects*, and add the member to the appropriate
    subprojects.

The user account we just created must be added to the following:

* All staff should be added to the appropriate *seniority group*. This
  automatically adds them to **Staff [Group]** and **Trusted [Group]**.
  All interns should be added to the **Interns [Group]** seniority group.

* Add the staff member to their *[Dept]* (department) project(s).
  (e.g. **Programming [Dept]**).

* If relevant, add the staff member to their *[Team]* project. At the moment,
  this only applies to the Programming department.

* Optionally, you may add them to the *[Project]* project(s) they are going
  to be working on. Technically, they can join these later, but being members
  automatically can save a lot of time and confusion.

..  NOTE:: It can be confusing for a new user to be blocked from accessing a
    repository because they aren't a project member. In general, it's just
    good practice to add new users to their expected projects.

The user can now log in and access everything they're supposed to.

Wrapping Up
-------------------------------------------

We're done! The new account is created and active across the entire staff
network. Login to all other Staff Network resources not mentioned is controlled
by LDAP.

You should send the username and password to the new user. Ideally, this should
be done in person, for maximum security. In that situation, cover the password
policies as described in the template email below.

If you need to send the password via email, use the following template::

    Welcome to MousePaw Media!

    Below are your login details for the entire Staff Network. Please write down
    your login information in a secure, physical location (not on your computer)
    and permanently delete this email, making sure to remove it from your trash
    as well.

    PASSWORD GUIDELINES

    Do not give out your login details under any circumstances. MousePaw Media
    IT staff maintain a secure database of all usernames and passwords, and we
    can also reset any and all passwords, so we will never ask you for your
    login information. We may need to access your company accounts from time to
    time for security maintenance purposes.

    Do not change your password. If you have forgotten your password, believe
    your account is compromised, or need a new password, contact us here at
    hawksnest@mousepawmedia.com, and we will assign you a new password.

    ---
    Email: <EMAIL HERE>

    Username: <USERNAME HERE>

    Password: <PASSWORD HERE>

    ---
    FURTHER INSTRUCTIONS

    You can access all aspects of this network from the staff portal at
    https://staff.mousepawmedia.com/. Use your full company email address to log
    into Webmail. For everything on DevNet, you can use just your username.

    You should next log into your company email, wherein you will find further
    instructions. All further communications will take place via your company
    email.

    If you have trouble logging in, please don't hesitate to contact the IT
    department at hawksnest@mousepawmedia.com.

Send this email to the user's personal email address, and then
**delete it from Sent Mail and Trash**! Ensure the email is *not* retained
anywhere on our servers.

..  NOTE:: You should encourage the new user not to use their browser's
    "save this password" feature. By typing their password reguarly, they
    will be able to remember it better more quickly.

Employee Account Suspension
===========================================

To lock an employee account, go to the LDAP control panel on Hawksnest:
`LDAP Account Manager <https://mousepawmedia.net/lam/>`_. Login using the
LDAP management password.

On the :guilabel:`Users` tab, look for the account you want to suspend.
Click the :guilabel:`Edit` icon (the pencil) toward the left of the row.

On the :guilabel:`Unix` tab, click :guilabel:`Lock password`.

Click :guilabel:`Edit groups` and remove all groups from the user. Add the
``former`` group, and click :guilabel:`Back`.

Then click :guilabel:`Save`. This will immediately lock down the account,
shutting off most access privileges and preventing access.

..  NOTE: You may need to unlock the password temporarily to access the account
    if you need to inspect as part of an investigation. If this is a
    possibility, consider changing the password as well.

On Phabricator, go to the user profile and :guilabel:`Manage`, and then
click :guilabel:`Disable User`.

On Nextcloud, click your profile picture in the upper-right corner and click
:guilabel:`Users`. Find the user you want to deactivate, and click the three
dots to the far right of the row. **Be certain you are acting on the right
user!!** Select :guilabel:`Wipe all devices` and confirm the unique ID number
against that near the username on the same screen. Click :guilabel:`Wipe` when
you are sure.

Click the three dots again, and click :guilabel:`Disable user`.

We also need to disable email from Postfix Admin.
(See :ref:`admin_accounts_new_email` to learn how to access that.)
Log in with your company account.

Under :guilabel:`Virtual List` select :guilabel:`Virtual List`. From the
drop-down box at the top, select :guilabel:`mousepawmedia.com`. Scroll down
to the list of email accounts, and look for the one you want to deactivate.
Click :guilabel:`Edit`.

Uncheck the :guilabel:`Active` box and click :guilabel:`Save changes`.

..  NOTE: You may need to reactivate the email account temporarily if you need
    to inspect as part of an investigation. If this is a possibility, consider
    changing the password as well.

External Accounts
===========================================
