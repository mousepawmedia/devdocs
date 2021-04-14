.. _glivecollab:

Guide: Live Collaboration
#########################################

We use **Visual Studio Code: Live Share** for live collaboration and pair
programming.

* You should have :ref:`vscode` installed and configured.
* You'll also need a GitHub account to login. If you don't have one,
  create one now.
* You should have headphones and a microphone for voice chat (ideally).

..  note:: In our experience, Live Share works well even on 200 Kbps internet
    connections.

The first time you start or join a session with Live Share, you will be
prompted to login to GitHub. Follow the prompts to do so.

.. _glivecollab_settings:

Settings
=========================================

Before using Live Share for the first time, you should check your Settings
in Visual Studio Code. Open up :menuselection:`File --> Preferences --> Settings`.
From the left pane of Settings, select
:menuselection:`Extensions --> Visual Studio Code Live Share configuration`.

I recommend the following, although you are encouraged to adjust these according
to your own preferences:

* :guilabel:`Account`: (Leave as is)
* :guilabel:`Account Provider`: (Leave as is)
* :guilabel:`Allow Guest Debug Control`: No
* :guilabel:`Allow Guest Task Control`: No
* :guilabel:`Anonymous Guest Approval`: ``prompt``
* :guilabel:`Auto Share Terminals`: Yes
* :guilabel:`Code Lens`: Yes
* :guilabel:`Comments`: Yes
* :guilabel:`Connection Mode`: ``auto``
* :guilabel:`Diagnostic Logging`: No
* :guilabel:`Feature Set`: ``stable``
* :guilabel:`Focus Behavior`: ``accept``
* :guilabel:`Guest Approval Required`: Yes
* :guilabel:`Increased Guest Limit`: No
* :guilabel:`Join Debug Session Option`: ``Automatic``
* :guilabel:`Keep Alive Interval`: 20
* :guilabel:`Allow Guest Command Control`: No
* :guilabel:`Name Tag Visibility`: ``Activity``
* :guilabel:`Populate Git Co Authors`: ``always``
* :guilabel:`Presence`: Yes
* :guilabel:`Publish Workspace Info`: Yes
* :guilabel:`Shared Terminal Height`: ``50``
* :guilabel:`Shared Terminal Height`: ``120``
* :guilabel:`Share External Files`: Yes
* :guilabel:`Show In Status Bar`: ``always``
* :guilabel:`Show Read Only Users In Editor`: ``always``
* :guilabel:`Show Verbose Notifications`: Yes

.. _glivecollab_start:

Starting A Session
=========================================

To start a session, in which you are the host, open the Live
Share pane by clicking its icon on your Activity Bar at left.

Select :guilabel:`Start collaboration session...`
to start a new session. Once the session is started, a link
will automatically be copied to your clipboard. Provide it to
whomever you wish to have join the session.

If you have previous collaborators under :guilabel:`Contacts`
on your Live Share pane, you can also invite them directly.
However over the name of someone you wish to invite, and click
the Invite Contact icon.

When you're done, you can stop the session from the Live Share pane.
Hover over :guilabel:`Session Details` at the top of the pane, and select
:guilabel:`Stop Collaboration Session`.

.. _glivecollab_join:

Joining A Session
=========================================

To join a session hosted by someone else, you must have the
Live Share URL they provided to you, or else you must be
invited directly through Visual Studio Code.

Open the Live Share pane by clicking its icon on your
Activity Bar at left.

Select :guilabel:`Join collaboration session...` to join a
session. If you've already copied the URL for the session to
your clipboard, it will automatically be pasted in the box that
pops up. Press :kbd:`Enter` to confirm.

It will take a few moments to join.

When you're done, you can leave the session from the Live Share pane.
Hover over :guilabel:`Session Details` at the top of the pane, and select
:guilabel:`Leave Collaboration Session`.

.. _glivecollab_tools:

Live Share Tools
==========================================

Through Live Share, guests can view and edit files (unless they're read-only),
control debugging sessions you've started, and even use the terminal (if you've
granted permissions). Since they're working through their own instance of VS
Code, they can even use their own keyboard shortcuts and (most) extensions while
editing, without touching your configuration.

There are a number of other tools available to you through Live Share.

.. _glivecollab_tools_chat:

Text Chat
-------------------------------------------

You can chat with the other people on the session. In the Live Share pane,
click :guilabel:`Chat Channel`.

.. _glivecollab_tools_audio:

Audio Call
-------------------------------------------

You can join an Audio Call by clicking :guilabel:`Audio Call` on the Live Share
pane. Using this is recommended, but not required.

.. _glivecollab_tools_inline:

Inline Comments
--------------------------------------------

You can leave comments inline on the code by hovering over the
line numbers on a file, and clicking the :guilabel:`+` button that
pops up wherever you want to start a comment thread.

Every line which has a comment will show a dot to the right of the line
number. Click the dot to view the comment. (Don't confuse this with breakpoints,
which are red dots to the *left* of the line numbers).

..  warning:: All of the inline comments will be deleted as soon as the
    session ends! Any comments you want to keep should be written as actual
    code comments in the file itself, *not* as Inline Comments.

.. _glivecollab_tools_following:

Following
---------------------------------------------

If you want to watch what another participant is doing, right click their
name under :guilabel:`Participants` at the top of the Live Share pane.
Then, click :guilabel:`Follow Participant` to have VSCode automatically scroll
and open files as the participant does.

Alternatively, if you want to follow someone without losing your own place,
select :guilabel:`Follow Participant to Side`. This will split the VSCode view
in half horizontally, and show what the other person is doing on the right.

To stop following a participant, select :guilabel:`Stop Following` from that
same menu.

.. _glivecollab_pair:

About Pair Programming
==========================================

At MousePaw Media, we encourage **pair programming** using Live Share.
This is a technique where two developers work together on the same code. It
provides an amazing opportunity to exchange knowledge, build communication
skills, and to get a fresh perspective on your code.

The Driver is the programmer doing most of the actual typing. The Navigator is
the programmer reviewing the code as it is typed, and providing live feedback.

The pair can work on either the driver or navigator's code, although they
should determine well in advance whose code sector to work on.

To make the most of your next pair programming session, I recommend reading
`7 Ways To Get The Most Out Of Pair Programming <https://dev.to/jacobherrington/7-ways-to-get-the-most-out-of-pair-programming-415h>`_

Staff can check the current MousePaw Media Pair Programming schedule
on the `Pair Programming Schedule wiki page <https://phabricator.mousepawmedia.net/w/programming/pair_schedule/>`_.

Happy collaborating!
