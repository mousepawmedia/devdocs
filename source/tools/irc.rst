.. _irc:

Freenode IRC
################################

IRC (Instant Relay Chat) is one of the oldest means of internet communication.
It is still widely used by programmers and ITs, especially because it lends
itself to "lurking": passively being in the room, occasionally glancing over to
see what's going on, and sometimes speaking up when the inclination strikes you.
You'd be amazed how much you can absorb from just from lurking in various
rooms (such as `#python`), not only about the language or technology in
question, but about programming in general!

IRC is one of the oldest and most widely used means of synchronous
communication among programmers. Most open-source organizations maintain
an IRC channel, including `#ubuntu`, `#synfig`, `#opus`, `#blender`, and so
forth. Of course, `#python` lives on that network too.

..  WARNING:: The `##c++` channel is infamously unfriendly. Avoid it. We're trying
    to start the ##c++-friendly room, so please consider lurking there.

.. _irc_setup:

Getting On
================================

.. _irc_setup_installing_hexchat:

Installing HexChat
--------------------------------

To use IRC, you need an IRC client. There are quite a few to choose from, many
of them FOSS.

..  NOTE:: While you can also access many IRC networks through a browser-based
    client, it isn't recommended, as many useful IRC tools don't work on those.

For Linux, one of the most popular IRC clients is **Hexchat**, which we will
install and set up in this section. If you've never used IRC before, I recommend
installing this client now. Once you're comfortable, you'll have the basic
IRC knowledge necessary to experiment with other options.

To install Hexchat on any Ubuntu-based distribution, run...

..  code-block:: bash

    $ sudo apt update
    $ sudo apt install hexchat

.. _irc_setup_choosing_network:

Choosing Your Network
--------------------------------

When you first start HexChat, it will bring up the Network List.

1)  Choose a Nick name. Freenode is HUGE, so many common names are taken, but
    you can frequently think up a good nick. Put it in for
    :guilabel:`Nick name` and :guilabel:`User name`.

2)  Under :guilabel:`Networks`, scroll down to `Ubuntu Servers (freenode)`
    (it's under "f"). Click :guilabel:`Edit...`

3)  Check :guilabel:`Connect to this network automatically`.

4)  Under :guilabel:`Login method:`, select `Server Password (/PASS password)`.

5)  Under :guilabel:`Password`, come up with a good password. Bear in mind that
    you may need to type it in plain text on occasion, so it shouldn't be
    something that you use for other important logins. **Write it down on paper!**

6)  Click :guilabel:`Close`.

7)  Check :guilabel:`Skip network list on startup`.

8)  Click :guilabel:`Connect`.

..  IMPORTANT:: If you come back to this dialog box later, you do NOT need to
    click :guilabel:`Connect` again. Instead, if you're already connected to
    Freenode, just click :guilabel:`Close`.

.. _irc_setup_register_nick:

Registering Your Nick
-----------------------------

The first time (and ONLY the first time) you connect to Freenode, you must
register your nickname. Go to the default "channel" marked `freenode`, which is
where you'll type the following commands as needed.

..  WARNING:: This is your personal account. DO NOT use your company email
    for the email address!

If you see a message that says "This nickname is registered. Please choose a
different nickname...", your chosen nickname is not available. To try another
nickname, type the following, where `NewNick` is the new nick you want to
try::

    /nick NewNickname

Once you find one that works, make sure you change it on
:menuselection:`HexChat --> Network List`, under both :guilabel:`Nick name` and
:guilabel:`User name`.

Once you have found a nickname that works, enter the following command.
Replace `me@example.com` with your email address, and `password` with your
IRC password (from the previous step)::

    /msg nickserv register password me@example.com

Go to the email address that you specified and follow the directions in the
email from "Freenode" to confirm your registration. If you don't see the
email in question, check your spam.

Assuming everything worked correctly, we can test it out. Go ahead and quit
Hexchat, and then start it again. It should log you in automatically.

To check if you ARE logged in, type the following, where `YourNick` is your
current nickname::

    /whois YourNick

If you're logged in, the second to last line should say "[YourNick] is
logged in as YourNick", where `YourNick` is your nickname.

.. _irc_setup_adding_nick:

Adding Other Nicks
---------------------------

You may want to register multiple nicknames with the same account. This is
very easy to do.

First, make sure you're logged in as your main nickname
(see :ref:`irc_setup_register_nick`). Then, switch to another nickname you want
to register by entering the following command, where `OtherNickname` is the new
nickname::

    /nick OtherNickname

Ensure that the nick is not already taken (you'll see a warning if it is). If
the nick is free, add it to your account via::

    /msg nickserv group

You may want to add your alternate nicks to Hexchat's settings. This is useful
if you try to connect to IRC while you're connected on another machine (it
happens more than you think!)

On HexChat, go to :menuselection:`HexChat --> Network List`, and your additional
nickname(s) under `Second choice` and `Third choice`. You can always swap out
the order in this list, depending on your preferences. Click `Close` when you're
done.

..  IMPORTANT:: It is NOT recommended practice to have "status nicks", such as
    "MyNick | Away". The nick changes sometimes spam channels you're in and
    drive the other participants crazy, so much so that a few channels have
    actually banned them. See the `Away Status` section below.

.. _irc_setup_preferences:

Preferences
-----------------------------

There are a number of fun and useful preferences in HexChat. Go to
:menuselection:`Settings --> Preferences`. Feel free to browse this, of course.
Here are a couple of recommendations.

Under :menuselection:`Chatting --> Logging`, check
`Enable logging of conversations to disk.` This will store text logs of all
your chats on your computer. On Linux, these are stored under
:file:`~/.config/hexchat/logs` by default.

Under :menuselection:`Chatting --> General`, you may want to set default
message for when you quit, leave a channel, or mark yourself as away. Keep
these brief and appropriate, but have fun with them!

.. _irc_using:

Using IRC
================================

.. _irc_using_networks:

Networks
--------------------------------

If you followed the above steps, you are now using the Freenode IRC network.
There are actually *many* IRC networks. You can manage your connections to
each on the `Network List` window of HexChat.

..  IMPORTANT:: You will need to register your nicks on each network you use.

.. _irc_using_channels:

Channels
--------------------------------

Conversations on IRC are primarily separated into Channels. There are hundreds
of channels on Freenode alone. Each channel name starts with at least one
`#` symbol.

To join our programming channel, for example, type::

    /join #mousepawgames

As soon as you join any channel, be sure to read the rules and information in
the **channel topic** at the top of the page, and in any initial login message
you receive.

Most channels have one or more **channel operators**, also knows as "mods" or
"ops". When someone is opped, HexChat will display a red dot next to their
name in the list. However, most ops (including our own) fly under the radar,
and don't "take op" until they need it.

Many channels also have **bots**, which are special computer programs that
perform automated tasks for the channel. For example, `Hawksnest` is the
official bot in `#mousepawgames` and `##mousepawgames`.

You can leave a channel by typing::

    /part #mousepawgames

...and then right-clicking the channel name in HexChat and clicking
:guilabel:`Close`. (You can also just close the channel without :code:`/part`,
but it's a little more abrupt.)

..  NOTE:: You can auto-join favorite channels in HexChat by right-clicking
    the channel name and checking :guilabel:`Autojoin`.

..  NOTE:: If you get sick of seeing notifications about users joining and
    parting,, right-click the channel name and check
    :menuselection:`Settings --> Hide Join/Part Messages`.

.. _irc_using_logging_in:

Logging In
------------------------------

You have already configured HexChat to log you in immediately. However, if you
happen to use another computer or a client that you cannot configure, don't
panic. You can log into Freenode IRC from anywhere in the world via the
following commands, where `YourNick` is your nickname, and `password` is your
IRC password::

    /nick YourNick
    /msg nickserv identify password

.. _irc_using_ghosting:

Ghosting
-------------------------------

Sometimes a network glitch will cause you to be disconnected from IRC, but your
nick to remain logged in. This can be a royal pain if you're trying to log
back in, as you'll be assigned your secondary nick.

If this happens, make sure you're logged in to IRC, and then run the following,
where `YourNick` is the nickname you want to reclaim::

    /msg nickserv ghost YourNick

.. _irc_using_general_chatting:

General Chatting
-------------------------------

Most of the time, you just type something and hit "enter" to talk in a channel.

If you want to get someone's attention in particular, you can **ping** them
(see :ref:`irc_netiquette_pinging`) just type their name, and HexChat will
alert them that they were mentioned. In HexChat, you have tab-completion on
usernames. In `#mousepawgames`, if Jason McDonald is online and you type "Co"
and hit tab, it'll likely autocomplete to "CodeMouse92".

:code:`/me` allows you to look like you're taking an action. For example, if
you type :code:`/me waves`, that shows up as "\*YourNick waves."

.. _irc_using_private_messaging:

Private Messaging
-------------------------------

:code:`/notice` allows you to whisper to someone else in the channel, so only
they see the message. For example, :code:`/msg CodeMouse92 Hi!` would whisper
"Hi" to that user, but only that user would be able to see it.

Using :code:`/query` opens a private chatroom (i.e. :code:`/query CodeMouse92`.
In HexChat, you can also right-click a username and click "Open Dialog" to open
a private chatroom with that person.

.. _irc_using_away:

Away Status
------------------------------

:code:`/away` marks you as away, and grays out your name on the user list. You
can optionally provide an away message. If you just type :code:`/away`, HexChat
will use the default message you set in Preferences.

:code:`/back` marks you as available, as in "no longer away."

Generally, it's a good idea to leave your HexChat window up throughout the day,
and just use :code:`/away` and :code:`/back` to show availability. This is
called "lurking", and it increases the chances you have of seeing things you'd
be interested in. (It also keeps logging stuff that happens in the channel while
you're not looking. You must be connected to IRC for logging to work.)

.. _irc_using_law_enforcement:

Law Enforcement
-------------------------

If you are an op in a room, you have the ability to perform various moderation
tasks. Most importantly, an op can kick people from a room, and can also
generally ban people to prevent them from coming back.

To be kickbanned means you're thrown out, and never allowed to return. As long
as you follow the rules, and a chatroom is relatively sane, this should be easy
to avoid.

In the `#mousepawgames` channel, some staff members have been given op status.
If you're an op, you can "take op" by typing::

    /msg chanserv op #mousepawgames

Then, you can kick and ban using the :code:`/kick nickname` and :code:`/ban
nickname` commands, respectively. Please use these powers wisely and
judiciously.

Once you're done with your op work, you can de-op via::

    /msg chanserv op #mousepawgames -nick_name

...where nick_name is your nickname. (Note the - in front.)

.. _irc_netiquette:

Netiquette
==============================

.. _irc_netiquette_cultural_sensitivity:

Cultural Sensitivity
-------------------------------

The most important thing to remember about IRC is that it is an international
platform. You will encounter people from all countries, cultures, and walks of
life. You should be respectful and culturally sensitive at all times. This
isn't really a matter of "political correctness"; you should simply communicate
in ways that are less likely to be misinterpreted.

On that note, if you do insult someone accidentally, just apologize and take
notice of how you could have communicated better. Intercultural communication
is an acquired skill, so most people will gently correct you. (Of course, if
someone is habitually insulted by everyone and everything, there's nothing you
can do about that.)

A good, quick check on your choice of phrase is to ask "does this assume
something about the other person?" Keep phrasing generic until you know
more about the person.

..  NOTE:: I have personally chatted with amazing people from all over the
    world - France, Spain, Germany, Scotland, Isle of Man, Mexico, Canada,
    Cyprus, Russia...and the list goes on! Most people enjoy talking about
    their country and culture, so there's never any harm in asking "where
    are you from?" (If they don't want to tell you, just let it go.)

Above all, **show everyone the respect you want others to treat you with.**

.. _irc_netiquette_asking_questions:

Asking Questions
----------------------------------

One of the main uses of IRC is for answering questions. These are some
*absolutely critical* rules you should follow for that:

* When asking your question, include all relevant software version/system
  information, code, and error messages. Use a paste tool for lots of text.
  (see :ref:`irc_netiquette_flooding_spamming`)

* Post your question and *wait*. Getting help on IRC is a crapshoot - a person
  with the knowledge to answer your question has to be present and active.
  If no one can help, typically no one will say anything to you.

* Refrain from posting your question again until it has disappeared off the
  screen some ways AND it has been more than ten minutes. People don't tend
  to 'read up' more than about a page or two (although there are exceptions).
  Once you're certain your question has been "buried", you may repost it.

* If you don't get an answer quickly, don't get impatient. Posting stuff like
  "Well?" or "ANYBODY??" doesn't help - it actually decreases the likelihood
  that someone will help you.

* STAY IN THE ROOM. Mark yourself as away if you have to, but as long as you
  are technologically *able* to stay in the room, do. It isn't uncommon to
  receive an answer several hours after asking the question. (That rule goes
  both ways, so don't feel bad if you miss a follow-up question by a few
  hours because you were away from the computer).

* Avoid cross-posting. Wait until your question is "buried" in a room, or until
  someone has expressly told you that no one can help you in that room, before
  posting it again on another room. If you wait a long time in a quiet room,
  it is generally okay to repost the question in a second room, but be sure to
  update the first room with the answer once you have it!

.. _irc_netiquette_debate:

Debate and "Room Temperature"
----------------------------------

Text-based communication is an odd medium. We naturally read tone and other
non-verbal cues into text. Even right now, you're almost certainly hearing
a "mental narrator" reading this passage in a particular tone (hopefully a
friendly one!)

This can quickly become a problem in IRC, especially when heated topics come
up. Some rooms even go as far as to ban religion and politics altogether, but
people *still* find things to fight about. We refer to the level of conflict
in a room as its **temperature**.

Controversial topics aren't necessarily *bad* within the appropriate time and
place; you can learn a lot by discussing culture and current events
with people around the world. In fact, it is possible to have these
conversations without causing problems. Here are a few rules:

* Put a check on your "mental narrator". Assume the best about the other
  person.

* Keep an eye on the "room temperature". If people are getting obviously
  upset, seriously consider dropping the conversation. Simply saying "This
  topic seems to be getting a bit heated. Let's talk about something else."
  goes a long way towards lowering the room temperature.

* If you feel your temper rising, *stop talking*.

* People are more important than "winning" a debate. It's okay to "agree to
  disagree", no matter what anyone says.

* If you are asked to drop a topic, **drop the topic**. Similarly, if you ask
  someone else to drop the topic, consider it dropped and refuse to engage
  further. (Ignoring an argument is a powerful tool.)

* If the debate is getting out of control, and you cannot escape it in the
  room, leave for a few minutes to cool down. Although a few would mislabel
  this as "rage-quitting," it is actually a mature way to handle exposive
  "no-win" situations. Check the room every few minutes to see if the debate
  has ended, and then rejoin. If you have to use this tactic, *never* resume
  the debate!

.. _irc_netiquette_pinging:

Pinging
-------------------------

When you include someone's nick in a message on IRC (assuming they're in the
same room as that message), they will be "pinged". This is a very useful
feature, but you should be careful not to abuse it.

* If you're in a large, busy room, you should almost always ping the person
  you're talking to on the first message in a chain. This helps the other
  person track the conversation.

* Only ping someone if you really want their attention. There are usually
  common shortened versions of nicknames (like "codemouse" instead of
  "CodeMouse92") that people will use to *refer* to a person without pinging
  them.

* If someone asks you not to ping them, *don't ping them*.

.. _irc_netiquette_private_messaging:

Private Messaging
-------------------------

It is possible to private message people on IRC. Most people are okay with
you just starting a private conversation, but if you're unsure, you should
ask first (either in the public room or in an initial private message).

Either way, respect the person and consider private messaging a privilege.
Some people are okay with occasional messages, but not long or frequent
conversations.

Of course, you have the same rights as anyone else in this matter. Be
polite but clear regarding your own private messaging boundaries.

.. _irc_netiquette_flooding_spamming:

Flooding and Spamming
----------------------------------

You should avoid posting large amounts of text. Even if you can seemingly fit
it all into the box on HexChat, IRC has a maximum message size, and HexChat
will trip what's in that box into the right number of pieces needed. Trying to
post lots of text will **flood** the room, which is a massive annoyance.

To avoid this, we use sites like `bpaste.net <http://bpaste.net>`_ for posting
code, error messages, and other long bits of text. Check the room topic
for the preferred pasting tool, defaulting to bpaste if nothing else is
requested.

Additionally, you should not post the same message over and over. This is
considered **spamming**, and it will usually get you warned and/or banned
from a room. (see :ref:`irc_netiquette_asking_questions`).

.. _irc_netiquette_trolling_flaming:

Trolling and Flaming
----------------------------------

Under no circumstances should you *ever* go into a room with the express
purpose of starting a debate or stirring up trouble, no matter how funny
you think it is. This is called **trolling**, and it is one the most grievous
crimes you can commit on IRC.

In general, you should also avoid using profanity and other offensive language.
Different rooms have different policies on this, but there's never harm in
keeping your language clean. (It also promotes use of more creative,
descriptive words - linguistically, the f-bomb is just meaningless filler.)

Excessive use of offensive or hurtful language, especially directed at a
person, is known as **flaming**. This is not okay. Ever. Period.

.. _irc_netiquette_bots:

Bots
--------------------------------

If the room you're in has a bot that is designed to be used by room
participants, it's a good idea to learn how to use it correctly. You should
never abuse a room's bot features.

There are also rogue chatbots, which are designed to automatically troll
channels. Since no one has ever beaten the Turing test, we can usually spot
these pretty quick. If, for some reason, someone begins questioning if you're a
bot, mix up your sentence structure - bots cannot do that very well.

.. _irc_netiquette_snark:

Sarcasm, Snark, and Pedantia
--------------------------------------

You have entered the single largest online community of nerds on the planet.
We have our own unusual way of communicating.

If someone sounds like they're insulting you, assume they're being sarcastic
or silly - they almost always are! Responding to an open insult as if it were
true and/or a compliment is the best way to respond. Those who insult
intentionally can't really work with that sort of response anyway.

Here's an example from my own chat logs. The other person actually didn't mind
my being there at all::

    *CodeMouse92 joins.
    [User1] Oh great... it's CodeMouse92

Beware double meanings and connotations! Computer programmers and ITs
especially like word play, so you're likely to have such a message
intentionally misinterpreted as a joke. Play along, or politely clarify
if necessary. For example::

    [phunyguy] well, my state is actually pretty up in the air right now so I said why not
    [phunyguy] ldunn, don't say it
    *CodeMouse92 notices that phunyguy did not ask HIM to not snark that statement
    [CodeMouse92] phunyguy, Your state is up in the air? I hope you're in a small one, otherwise you're going to have a hard time finding an airfield big enough.
    [phunyguy] CodeMouse92, oh so it was YOU that said it. I thought ldunn and Flannel would be the ones.

While it may be tempting to join in on this sort of conversation right away,
**do not try this at home**...at least, not until you have had enough
experience in a room to know the social expectations and general rhythm.
