"Hawksnest" IRC Bot
###############################

The :code:`#mousepawgames` and :code:`##mousepawgames` Freenode IRC chatrooms
are patrolled by our bot, **LukeDMouse**.

..  NOTE:: You may remember the prior bot, Hawksnest. That bot was run by the
    Phabricator chatbot software, which has been discontinued due to a
    security flaw.

Commands
========================================

* :code:`!coin`: Flips a coin.

* :code:`!dice <n>d<sides>`: Rolls ``<n>`` dice with ``<sides>``. For example,
  `!dice 2d10` rolls two ten-sided dice.

* :code:`!seen <nick>`: Lists the last time the user ``<nick>`` was seen in the
  room by the bot, and what they last said.

* :code:`!tell <nick> <text>`: Tells the user ``<nick>`` whatever message you
  specify when they next log in.

Learning
=========================================

Logging
========================================

The bot also logs the rooms. We are working on a way to make these logs
publicly accessible.
