..  _admin_accounts:

Account Management
##########################################

..  NOTE:: Section coming soon.

..  _admin_accounts_new:

Employee Account Creation
===========================================

All company accounts are managed (primarily) from our LDAP server, with
three notable exceptions.

* Postfix manages email accounts, so we can create additional addresses.

* eHour manages its own accounts, allowing certain volunteers to log time.

* Tao Testing manages its own accounts, allowing volunteers to access quizzes.

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

Next, we need to create an Alias, so emails to the equivalent
:code:`@mousepawgames.com` address will automatically forward. To create
this, select :menuselection:`Virtual List --> Add Alias`. Fill out the
following:

* :guilabel:`Alias`: This should match the username on the email account we just
  created. Select the :code:`mousepawgames.com` domain name from the drop-down
  menu below this field.

* :guilabel:`To`: This is a list of the addresses to forward email to, one
  address per line. We only need one, so enter the equivalent
  :code:`@mousepawmedia.com` email address.

* :guilabel:`Active`: Make sure this is *checked*.

Click :guilabel:`Add Alias` to create the alias.

You should also review your settings in
:menuselection:`Virtual List --> Virtual List`. Select the correct domain
from the drop-down box to view all email addresses and aliases. Ensure the
new mailbox exists under :code:`mousepawmedia.com`, and the corresponding
alias under :code:`mousepawgames.com`.

In a new tab, make sure you can log into webmail at
`webmail.mousepawmedia.com <https://webmail.mousepawmedia.com/src/login.php>`_
using the new email address and password.

Once you've confirmed the account was created correctly, select
:guilabel:`Logout` from the top menu on Postfix Admin.

Be sure to have IT disable the control panel via...

..  code-block:: bash

    $ controls off

..  _admin_accounts_new_ldap:

Creating LDAP Account
-------------------------------------------

..  WARNING:: The LDAP control panel can only be accessed from the same local
    network as the server.

To create a new LDAP account, go to the LDAP control panel on Hawksnest:
`phpLDAPadmin <https://mousepawmedia.net/phpldapadmin/>`_. Login using the
LDAP management password.

On the left tree, expand the sole top level entry. Expand :guilabel:`ou=Users`
and select :guilabel:`Create new entry here` from the subtree.

On the main pane, select :guilabel:`Generic: User Account`. Fill out the
following fields *in the order listed*. Leave all others blank.

* :guilabel:`GID Number`: :code:`staff`

* :guilabel:`Last name`: The full name - first name, middle initial, last name.

* :guilabel:`Common Name`: The company username.

* :guilabel:`Password`: The password for the user. Be sure to confirm it.

* :guilabel:`User ID`: The company username (yes, again).

Press the :guilabel:`Create Object` button. On the next screen, make sure the
fields you specified are accurate, and then click :guilabel:`Commit`.

On the tree at left, select the new account. On the main pane, click
:guilabel:`Add new attribute`. On the drop-down menu that appears, select
:guilabel:`Email` (the entries aren't sorted alphabetically). Enter
the user's company email address, as created in the prior step. Then scroll
to the bottom of the page and click :guilabel:`Update Object`.

Double check the change on the new screen, and then click
:guilabel:`Update Object` to save your changes.

The account is created, and can be used immediately. We can now close out
of phpLDAPadmin.

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

We still need to modify the account, but we can do this by temporarily
designating it a "bot" account, giving an administrator full control over
the profile.

A company IT with root SSH access to Hawksnest should run the following...

..  code-block:: bash

    cd /opt/phab/phabricator/bin
    sudo ./accountadmin

On that program, they must enter the username for the account we're wanting
to modify. When asked if we're modifying the existing account, have them
confirm :code:`Y`. Press :kbd:`ENTER` for each field to use the current
values, *except*, have them answer :guilabel:`Is this user a bot? [y/N]` with
:code:`y`. Finally, confirm that we want to save changes.

A Phabricator administrator may now access the People application through
Phabricator's web interface, and select the newly created account.
On the left, click :guilabel:`Manage`.

On that page, click :guilabel:`Edit Profile`. Change the title to the user's
appropriate title (i.e. "Junior Developer" or "Apprentice Designer"). You may
also want to specify the School, since we use this for reference. Leave
the rest of the fields at their defaults, so the user can set up their own
profile. Scroll to the bottom of the page and click :guilabel:`Save Profile`.

Next, select :guilabel:`Edit Profile Picture` and set the user's picture.
We always crop profile pictures to 300x300 PNG images.

Finally, select :guilabel:`Edit Settings`. Change the :guilabel:`Pronoun` to
the appropriate value and click :guilabel:`Save Changes`.

We're now done changing the user profile. Have the company IT run...

..  code-block:: bash

    cd /opt/phab/phabricator/bin
    sudo ./accountadmin

As before, they should specify the username, and keep all values as they
are, *except* now changing :guilabel:`Is this user a bot? [y/N]` to
:code:`n`. Confirm that we want to save changes. The account is now back
to default.

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

..  _admin_accounts_new_ehour:

New eHour Account
-------------------------------------------

To create a new account on eHour, you must log in with an eHour manager
account. Go to :menuselection:`Manage --> Users`. Click :guilabel:`Add User`
towards the top middle of the page. Fill out the following fields:

* :guilabel:`Username`: The company username for the new user.

* :guilabel:`First name`: The first name and middle initial (e.g. "Jason C.").

* :guilabel:`Last name`: The last name (e.g. "McDonald").

* :guilabel:`E-Mail`: The new user's company email address.

* :guilabel:`Password`: The new user's company password.

* :guilabel:`Confirm password`: Confirm the password (obviously).

* :guilabel:`Department`: Select :code:`Staff`.

* :guilabel:`User roles`: By default, users should only have the :code:`User`
  role. The other three are only for management.

..  NOTE:: All users with the :code:`User` role can report their own time,
    and can view their own time records.

* :guilabel:`Active`: Be sure this is *checked*.

* :guilabel:`Assign to project after save?`: This should be *checked*.

Click :guilabel:`Save`.

On the next screen, the user will automatically be assigned to the only
project in our system. We don't need to do anything else here.

We should test out the account by signing into eHour with the new username
and password. If that works, we're done here!

..  _admin_accounts_new_tao:

New Tao Testing Account
-------------------------------------------

Finally, we must add a new account on Tao Testing, so the new user can take
quizzes and tests.

Log into Tao Testing at
`quiz.mousepawmedia.net <https://quiz.mousepawmedia.net/tao/Main/login>`_
with an administrator account. When prompted, click
:guilabel:`Enter TAO Back Office`.

On the top menu, click :guilabel:`Test-takers`. On the left pane, click
:guilabel:`New test-taker`. Fill out the following values:

* :guilabel:`Label*`: The company username for the new user.

* :guilabel:`First Name`: The first name and middle initial for the new user
  (e.g. "Jason C.").

* :guilabel:`Last Name`: The last name for the new user (e.g. "McDonald").

* :guilabel:`Mail`: The company email for the new user.

* :guilabel:`Interface Language*`: "English" is required for all users, as
  the tests are only in this language.

* :guilabel:`Login*`: The company username for the new user.

* :guilabel:`Password*`: The company password for the user.

* :guilabel:`Repeat password*`: Confirm the password (obviously).

Click :guilabel:`Save` below the form.

Select the new user in the left pane. On the right pane, check the "TestTakers"
group, and click :guilabel:`Save`.

At this point, you should test the Tao login. If it works, we're done here.

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

External Accounts
===========================================
