..  _admin_accounts:

Account Management
##########################################

..  note:: Section coming soon.

..  _admin_accounts_new:

Staff Account Creation
===========================================

In most places, we use our GitLab instance as our Single-Sign On provider.
At present, only the email accounts are managed separately.


..  _admin_accounts_new_names:

Username Conventions and Considerations
-------------------------------------------

Usernames are the given name initial(s) and surname.
For example, Nafula A. Jackson would be "najackson", and Jovil Benoit
(no middle name) would be "jbenoit". If someone has multiple middle
initials, use all of them.

Addtionally, in cultures where there are multiple surnames, consult with the
individual on which to use, or whether you should use both. This occurs
frequently with Latin American names, in which case we use the first surname.
Maria Pilar Flores Mendoza likely goes by Maria Flores, according to Latin
American tradition (but check with her!), so her username would probably be
"mpflores".

Asian names typically work opposite of how they do in western culture: the
surname (family name) comes first, followed by the given name(s).
However, many people with Asian names living in western cultures will invert
their name to prevent confusion. Check with the individual to understand what
is their given name, and what is their surname. For example, Myeong Sun Jung
would probably be either "smyeong" or "sjmyeong", depending on her preference.
However, she may originally present her westernized name as Sunjung Meyong, as
Sun Jung is her entire "first name", so she may combine them and put them first
to prevent people from calling her "Sun". or "Myeong".
Always ask instead of assuming!

In addition to getting the username right, be sure you confirm the preferred
format for the full name!

..  _admin_accounts_new_email:

Creating Email Account
-------------------------------------------

Go to `mail.mousepawmedia.com <https://mail.mousepawmedia.com>`_ and log in
with an administrator account.

Go to :guilabel:`Mailboxes` -> :guilabel:`Mailboxes`, and click
:guilabel:`Add Mailbox`.

* :guilabel:`Username`: (the username)
* :guilabel:`Domain`: :code:`mousepawmedia.com`
* :guilabel:`Full name`: (the full name)
* :guilabel:`Password`: (click :guilabel:`Generate` to create a temporary password)
* :guilabel:`Force password update at next login`: Yes
* :guilabel:`Grant direct login access to SoGo`: Yes

Save the temporary password, and click :guilabel:`Add` at the bottom of the
screen to create the account.

..  _admin_accounts_new_notify:

Creating Email Account
-------------------------------------------

Once you've created the account, send an email from `it@mousepawmedia.com`
to the personal email address of the account holder. Use the following
template, substituting all values in :code:`<corner brackets>` below.

..  code-block:: text

    Dear <NAME>,

    Welcome to MousePaw Media!

    Below are your login details for your MousePaw Media account.

    ---

    Email: <MOUSEPAW MEDIA EMAIL ADDRESS>
    Username: <USERNAME>
    Password: <TEMPORARY PASSWORD>

    ---

    1. Log in at https://mail.mousepawmedia.com/ and change your password.

    2. Use the email sent to that address to log in at https://gitlab.mousepawmedia.com.

    3. Go to https://gitlab.mousepawmedia.com/-/profile/account to change your password and add Two-Factor Authentication.

    4. Log in to https://discourse.mousepawmedia.com and log in with your GitLab account.


    PASSWORD GUIDELINES

    1. Do not give out your login details under any circumstances. MousePaw Media IT staff can manage all company accounts and reset passwords, so we will never ask you for your login information.

    2. Use strong passwords which are at least 16 characters in length, have a mix of lowercase and uppercase letters, numbers, and symbols.

    3. Use Two-Factor Authentication whenever available.

    4. We strongly recommend using an encrypted password manager, such as BitWarden, for storing your credentials. Do NOT use your browser's built-in password manager!

    If you have trouble logging in, please don't hesitate to contact us here at it@mousepawmedia.com.

After sending that email, IMMEDIATELY proceed with the next step, so the new
user will not be delayed in logging in.

..  _admin_accounts_new_gitlab:

Creating GitLab Account
-------------------------------------------

On GitLab, log in with an admin account. On the left, click
:guilabel:`Search or go to...` and click :guilabel:`Admin Area`. You may
need to reauthenticate for security reasons.

On the left, select :guilabel:`Users`, and click the blue :guilabel:`New User`
button on the right of that screen.

Enter the following information:

* :guilabel:`Name`: (the full name)
* :guilabel:`Username`: (the username)
* :guilabel:`Email`: (the @mousepawmedia.com email created in the previous step)
* :guilabel:`Access Level`: :guilabel:`Regular`
* :guilabel:`External`: No
* :guilabel:`Validate user account`: Yes

At the bottom, click :guilabel:`Create user`. An email will be sent to the
specified MousePaw Media email address with a link to reset the GitLab password
and log in.

You **MUST** add the user to the Staff group for them to access some other
parts of the staff network. To do this, from the main page of GitLab, click
:guilabel:`Groups` on the sidebar, and click :guilabel:`Staff` on the group
list.

On the sidebar for the group, click :guilabel:`Manage` and :guilabel:`Members`.
Click :guilabel:`Invite members` in the upper-right corner, and enter the
username of the user you want to add. Select the role :code:`Developer`.
(We reserve :code:`Owner` for admins only.) Then click :guilabel:`Invite`.
The user will be added to the Staff group.

Additionally, add them as :code:`Developer` to the other groups for which
they should have access:

* Applications: for Applications (Software Engineering) department.
* Content Development: for Content Development department.
* Designers: for Design department.
* DevOps: for DevOps (Software Engineering) department.
* Platform: for Platform (Software Engineering) department.

Additionally, **only** for managers, add the following if appropriate:

* Hiring: grants read access to hiring records.
* Librarians: grants ability to manage entire eBook collection on Nextcloud.
* Management: access to all management records, and admin abilities on Nextcloud.

..  _admin_accounts_new_discourse:

Discourse Account
-------------------------------------------

Our Discourse instance's account management is delegated entirely to
our GitLab instance. Users will need to sign up there with their GitLab account
*before* you can grant them permissions.

Once they've created their account, go to :guilabel:`Admin` and
:guilabel:`Users`. Click :guilabel:`New` to sort by newly created accounts,
and find and click on the user's account.

For staff, scroll down to the :guilabel:`Permissions` section. Click
:guilabel:`Grant Moderation`. Set :guilabel:`Trust Level` to
:guilabel:`4: leader` and click the green check mark.

..  note:: we grant moderator privileges to all staff members. This makes it
    easier to maintain our community.

..  _admin_accounts_new_nextcloud:

Nextcloud Account
-------------------------------------------

If you added the user's GitLab account to the Staff group, they will have
access to Nextcloud automatically. Their name, profile picture, groups, and
(public) email address will be copied from GitLab to Nextcloud every time
they log in. Group membership is automatic, and is syncronized to the
GitLab groups the user is a member of.

..  _admin_accounts_suspend:

Account Suspension and Control
===========================================

It is possible to suspend any account, staff or external, temporarily or
permanently.

..  _admin_accounts_suspend_email:

Suspend Email
-------------------------------------------

Go to `mail.mousepawmedia.com <https://mail.mousepawmedia.com>`_ and log in
with an administrator account.

Go to :guilabel:`Mailboxes` -> :guilabel:`Mailboxes`, and click :guilabel:`Edit`
next to the email address you wish to suspend.

Towards the bottom, find the drop-down box that says :guilabel:`Active`, and
change it to :guilabel:`Inactive`. Uncheck the box for
:guilabel:`Grant direct access to SOGo`. Click :guilabel:`Save changes`.

..  NOTE: You may need to reactivate the email account temporarily if you need
    to inspect as part of an investigation. If this is a possibility, consider
    changing the password as well.

..  _admin_accounts_suspend_gitlab:

Block GitLab Account
-------------------------------------------

On GitLab, log in with an admin account. On the left, click
:guilabel:`Search or go to...` and click :guilabel:`Admin Area`. You may
need to reauthenticate for security reasons.

On the left, select :guilabel:`Users`. On that list, find the user you wish
to suspend, and click the three dots to the far right of the username. You
have two options:

* **Block**: Use this option to suspend an account without hiding contributions.
  We recommend using this option in most cases.

* **Ban User**: Use this option to Block *and* to hide the user's contributions
  from all other users. **Only use this option if the user's content was broadly
  abusive.**

..  note:: We STRONGLY advise you DO NOT use the Delete User option, except
    where an account's content is entirely abusive.

..  _admin_accounts_suspend_discourse:

Suspend Discourse Account
-------------------------------------------

If you Block or Ban User on GitLab, that will prevent logging in on Discourse.
However, if you want to separately control Discourse access, that is possible
as well.

On Discourse, click :guilabel:`Admin` on the left, and select :guilabel:`Users`
from the top navigation bar.

Click the username of the user you wish to suspend.

Scroll down to :guilabel:`Permissions.` You have a few options:

* **Trust Level**: You can lower trust level to limit permissions, and can
  :guilabel:`Lock Trust Level` if necessary to prevent the user from regaining
  those privileges until unlocked. This is useful if you want to allow a user
  to continue to access Discourse, but be limited in what actions they can take.

* **Revoke Moderator**: Remove access to staff-only categories, but otherwise
  allow the user to remain active on Discourse. You must also take this
  action before Suspending (below).

* **Suspended**: Prevent access to Discourse directly. Useful if you still
  want to allow access to GitLab.

* **Silenced**: Prevent posting or starting topics. Still has read permissions.
